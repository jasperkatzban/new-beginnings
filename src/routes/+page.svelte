<script lang="ts">
	import { writable } from 'svelte/store';
	import { goto } from '$app/navigation';
	import { page } from '$app/state';
	import Prando from 'prando';

	import ideas from '$lib/ideas.js';

	const numIdeas = ideas.length;

	const today = new Date();
	let dateSeed = [today.getUTCDate(), today.getUTCMonth(), today.getUTCFullYear()].toString();
	let rng = new Prando(dateSeed);

	const ideaIndex = writable(
		page.url.searchParams.get('idea')
			? parseInt(page.url.searchParams.get('idea') || '')
			: Math.floor(rng.next(0, numIdeas))
	);

	$inspect(ideaIndex);

	const randomizeIdeas = () => {
		ideaIndex.set(Math.floor(Math.random() * numIdeas));
		goto('?idea=' + $ideaIndex.toString(), {
			replaceState: true,
			noScroll: true,
			keepFocus: true
		});
	};

	$inspect(ideaIndex);
</script>

<div class="flex min-h-screen flex-col items-center justify-center px-4 py-6 text-slate-900">
	<div
		class="bg-white-95 align-items-center flex h-[50vh] max-h-[60vh] min-h-[40vh] w-full max-w-[90vw] justify-items-center border-slate-300 ring sm:max-w-[80vw] md:max-w-180 lg:max-w-225"
	>
		<h1 id="responsive-textbox" class="m-auto p-6 text-center">
			{ideas[$ideaIndex].text}
		</h1>
	</div>
	<div class="mt-6 flex gap-4">
		<!-- <button class="h-12 w-12 rounded-full border border-slate-300 hover:bg-slate-100" /> -->
		<button
			class="h-12 w-12 rounded-full border border-slate-300 hover:bg-slate-100"
			on:click={randomizeIdeas}
		/>
		<!-- <button class="h-12 w-12 rounded-full border border-slate-300 hover:bg-slate-100" /> -->
	</div>
</div>
