<script lang="ts">
	import { writable } from 'svelte/store';
	import { goto } from '$app/navigation';
	import { page } from '$app/state';
	import Prando from 'prando';

	import ideas from '$lib/ideas.js';
	import randomizeIcon from '$lib/assets/randomizeIcon.svg';
	import waveFrame from '$lib/assets/wave.svg';

	const numIdeas = ideas.length;

	const today = new Date();
	let dateSeed = [today.getUTCDate(), today.getUTCMonth(), today.getUTCFullYear()].toString();
	let indexSelector = new Prando(dateSeed);

	const ideaIndex = writable(
		page.url.searchParams.get('idea')
			? parseInt(page.url.searchParams.get('idea') || '')
			: Math.floor(indexSelector.next(0, numIdeas))
	);

	const idea = $derived(ideas[$ideaIndex]);

	const backgroundColors = [
		'bg-orange-600',
		'bg-yellow-600',
		'bg-green-600',
		'bg-emerald-600',
		'bg-indigo-600',
		'bg-purple-600',
		'bg-pink-600',
		'bg-fuchsia-600',
		'bg-teal-600',
		'bg-rose-600'
	];

	const backgroundColor = writable(
		backgroundColors[Math.floor(Math.random() * backgroundColors.length)]
	);

	const randomizeIdeas = () => {
		ideaIndex.set(Math.floor(Math.random() * numIdeas));
		backgroundColor.set(backgroundColors[Math.floor(Math.random() * backgroundColors.length)]);
		goto('?idea=' + $ideaIndex.toString(), {
			replaceState: true,
			noScroll: true,
			keepFocus: true
		});
	};
</script>

{#snippet frameCenter()}
	<div id="content-area" class="flex h-full grow border-2 border-black bg-white">
		<h1
			id="responsive-textbox"
			class="m-auto p-10 text-center text-2xl font-bold sm:text-3xl md:text-4xl lg:text-5xl {idea.font} leading-10"
		>
			{idea.text}
		</h1>
	</div>
{/snippet}

{#snippet frameCorner()}
	<div class="size-14 flex-none border-2 border-black bg-white md:size-28">
		<img src={waveFrame} class="h-full w-full" />
	</div>
{/snippet}

{#snippet frameHorizontal()}
	<div class="flex grow border-2 border-black bg-white">
		<div class="h-full grow">
			<img src={waveFrame} class="h-full" />
		</div>
		<div class="h-full w-14 shrink-0 md:w-28">
			<img src={waveFrame} class="h-full" />
		</div>
		<div class="h-full grow">
			<img src={waveFrame} class="h-full" />
		</div>
	</div>
{/snippet}

{#snippet frameVertical()}
	<div class="h-full w-14 flex-none border-2 border-black bg-white md:w-28">
		<div class="h-full w-full">
			<img src={waveFrame} class="h-full w-full" />
		</div>
	</div>
{/snippet}

<div
	class="flex min-h-screen flex-col items-center justify-center {$backgroundColor} transition duration-500"
>
	<div
		id="frame"
		class="mt-24 flex h-full max-h-[80vh] w-full max-w-[90vw] grow flex-col bg-black ring-2 ring-black sm:max-w-[80vw] md:max-w-180 lg:max-w-225"
	>
		<div class="flex h-14 w-full md:h-28">
			{@render frameCorner()}
			{@render frameHorizontal()}
			{@render frameCorner()}
		</div>
		<div class="flex size-28 w-full grow">
			{@render frameVertical()}
			{@render frameCenter()}
			{@render frameVertical()}
		</div>
		<div class="flex h-14 w-full md:h-28">
			{@render frameCorner()}
			{@render frameHorizontal()}
			{@render frameCorner()}
		</div>
	</div>
	<div class="mt-6 mb-6 flex gap-4">
		<!-- <button class="h-12 w-12 rounded-full border border-slate-300 hover:bg-slate-100" /> -->
		<button
			id="randomize-button"
			class="h-12 w-12 rounded-full border-4 border-black bg-gray-800 opacity-50 transition duration-300 hover:opacity-100"
			on:click={randomizeIdeas}
		>
			<img src={randomizeIcon} />
		</button>
		<!-- <button class="h-12 w-12 rounded-full border border-slate-300 hover:bg-slate-100" /> -->
	</div>
</div>

<!-- <div>
	<p class="font-0">a</p>
	<p class="font-1">a</p>
	<p class="font-2">a</p>
	<p class="font-3">a</p>
	<p class="font-4">a</p>
	<p class="font-5">a</p>
	<p class="font-6">a</p>
	<p class="font-7">a</p>
</div> -->
