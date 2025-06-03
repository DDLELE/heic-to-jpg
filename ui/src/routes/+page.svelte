<script lang="ts">
	import { addToast } from '$lib/toastStore';
	import TreeWrapper from '../lib/components/treeWrapper.svelte';

	import { apiService } from '$lib/requests';
	import { onMount } from 'svelte';
	import Spinner from '$lib/components/Spinner.svelte';

	let info: any | undefined;
	onMount(async () => {

	});
</script>

<div>
	<h1 class="text-xl text-center p-2">
		<div class="flex flex-row items-center justify-center">

	Hi Mom</h1>


</div>
