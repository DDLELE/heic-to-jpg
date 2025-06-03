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

	async function handle() {
		if(!files.files || files.files.length === 0)
		{
			errorToast("select some HEIC images first!")
			return
		}
		const zip = new JSZip();
		let worth = false
		for (const file of files.files ?? []) {
			infoToast(`processing ${file.name}`)
			try {
				const jpeg = await heic({
					blob: file,
					type: 'image/jpeg',
					quality: quality / 100
				});
				console.log(jpeg);
				zip.file(file.name.replaceAll('.heic', '.jpg'), jpeg);
				worth = true
			} catch (error: any) {
				console.log(error)
				errorToast(`failed converting ${file.name}: ` + error, true, 1000);
			}
		}
		if(!worth)
		{
			return
		}
		infoToast("generating zip")
		const res = await zip.generateAsync({ type: 'blob' });
		saveBlob(res, 'images.zip');
	}
</script>

<h1 class="text-3xl text-center m-5">HEIC to JPG converter</h1>
<h2 class="text-md text-center mb-20">offline / free / private</h2>
<div class="p-10 flex flex-col space-y-5 items-center justify-center">
	<div class="flex flex-row items-center justify-center">
		<input type="file" bind:this={files} multiple />
	</div>
	<div class="m-2 flex flex-row space-x-5 justify-around">
		<p>out quality</p>
		<input type="range" bind:value={quality} />
		<p>{quality}</p>
	</div>
	<button class="bg-stone-600 p-3" on:click={handle}>Convert</button>
</div>
