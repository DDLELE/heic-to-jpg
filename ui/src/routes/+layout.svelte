<script lang="ts">
	import { staticConfig } from '$lib/config';
	import './styles.css';
	import Toasts from '$lib/components/Toasts.svelte';
</script>

<svelte:head>
	<title>{staticConfig.name}</title>
</svelte:head>

<Toasts />
<div class="flex flex-row min-h-[100vh]">

	<main class="w-full h-full">
		<div
			class="flex flex-col m-8 shadow shadow-stone-300 dark:shadow-zinc-800 bg-white rounded-xl dark:bg-[#1E2021] dark:text-stone-50"
		>
			<slot />
		</div>
	</main>
</div>

<style>
</style>
