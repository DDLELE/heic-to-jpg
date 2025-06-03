<script lang="ts">
	import { errorToast, infoToast } from '$lib/toastStore';
	import { error } from '@sveltejs/kit';
	const heic = (globalThis as any).HeicTo;
	let files: HTMLInputElement;

	import JSZip from 'jszip';

	function saveBlob(blob: any, fileName: string) {
		var a = document.createElement('a');
		document.body.appendChild(a);
		a.style = 'display: none';

		var url = window.URL.createObjectURL(blob);
		a.href = url;
		a.download = fileName;
		a.click();
		window.URL.revokeObjectURL(url);
	}
	let quality: number = 70;

	let current = 0;
	let max = 0;

	async function processFile(zip: any, file: any) {
		try {
			console.log(`processing ${file.name}`);
			const jpeg = await heic({
				blob: file,
				type: 'image/jpeg',
				quality: quality / 100
			});
			zip.file(file.name.replaceAll('.heic', '.jpg'), jpeg);
		} catch (error: any) {
			console.log(error);
			console.log(`failed converting ${file.name}: ` + error, true, 1000);
		}
	}

	async function handle() {
		if (!files.files || files.files.length === 0) {
			errorToast('select some HEIC images first!');
			return;
		}
		const zip = new JSZip();
		current = 0;
		max = files.files.length;
		let worth = false;
		let awaitable: any[] = [];
		let batch = 0;
		for (const file of files.files ?? []) {
			batch++;
			current++;
			awaitable.push(processFile(zip, file));
			if (batch == 5 || current > max - 5) {
				infoToast(`processing ${current} / ${max}`);
				console.log(`processing ${batch} items`)
				const settled = await Promise.allSettled(awaitable);
				batch = 0;
				worth = worth || settled.find((i) => i.status === 'fulfilled') != undefined;
				awaitable = []
			}
		}
		if (!worth) {
			return;
		}
		infoToast('generating zip');
		const res = await zip.generateAsync({ type: 'blob' });
		saveBlob(res, 'images.zip');
		current = 0;
	}
</script>

<h1 class="text-3xl text-center m-5">HEIC to JPG converter</h1>
<h2 class="text-md text-center mb-20">offline / free / private</h2>
<div class="p-10 flex flex-col space-y-5 items-center justify-center">
	<div class="flex flex-row items-center justify-center">
		<input type="file" bind:this={files} multiple />
	</div>
	<div class="m-2 flex flex-row space-x-5 justify-around">
		<p>quality</p>
		<input type="range" bind:value={quality} />
		<p>{quality}</p>
	</div>
	<progress class="w-full" value={current} {max}> {Math.round((current / max) * 1000)}% </progress>
	<button class="bg-stone-600 p-3" on:click={handle}>Convert</button>
</div>
