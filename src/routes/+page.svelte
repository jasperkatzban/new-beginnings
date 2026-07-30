<script lang="ts">
	import { writable } from 'svelte/store';
	import { goto } from '$app/navigation';
	import { page } from '$app/state';

	import Prando from 'prando';

	import ideas from '$lib/ideas.js';
	import randomizeIcon from '$lib/assets/randomizeIcon.svg';
	import * as imgSrcs from '$lib/assets/frame/';

	type imgSrcKey = keyof typeof imgSrcs;
	const frameCornerImgSrcs = writable({
		tl: '' as imgSrcKey,
		tr: '' as imgSrcKey,
		bl: '' as imgSrcKey,
		br: '' as imgSrcKey
	});

	const randomizeFrameCorners = () => {
		let possibleFrameCornerKeys = Object.keys(imgSrcs).filter((key) => key.startsWith('corner'));
		let i = getRandomIndex(possibleFrameCornerKeys);
		const tl = possibleFrameCornerKeys.splice(i, 1)[0] as imgSrcKey;
		i = getRandomIndex(possibleFrameCornerKeys);
		const tr = possibleFrameCornerKeys.splice(i, 1)[0] as imgSrcKey;
		i = getRandomIndex(possibleFrameCornerKeys);
		const bl = possibleFrameCornerKeys.splice(i, 1)[0] as imgSrcKey;
		i = getRandomIndex(possibleFrameCornerKeys);
		const br = possibleFrameCornerKeys.splice(i, 1)[0] as imgSrcKey;

		frameCornerImgSrcs.set({
			tl: tl,
			tr: tr,
			bl: bl,
			br: br
		});
	};

	const getRandomIndex = (possibleFrameCornerKeys: string[]) => {
		return Math.floor(Math.random() * possibleFrameCornerKeys.length);
	};

	randomizeFrameCorners();

	const decorationIndex = writable(1);

	const randomizeDecoration = () => {
		const oldDecorationIndex = $decorationIndex;
		while ($decorationIndex === oldDecorationIndex) {
			decorationIndex.set(Math.floor(Math.random() * 4) + 1);
		}
	};

	randomizeDecoration();

	const getImgSrcKey = (mode: string) => {
		return (mode + $decorationIndex.toString()) as imgSrcKey;
	};

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
		const oldColor = $backgroundColor;
		while ($backgroundColor === oldColor) {
			backgroundColor.set(backgroundColors[Math.floor(Math.random() * backgroundColors.length)]);
		}
		goto('?idea=' + $ideaIndex.toString(), {
			replaceState: true,
			noScroll: true,
			keepFocus: true
		});
	};

	const newIdea = () => {
		randomizeFrameCorners();
		randomizeDecoration();
		randomizeIdeas();
	};
</script>

{#snippet frameCenter()}
	<div id="content-area" class="flex h-full grow border border-black bg-white">
		<h1
			id="responsive-textbox"
			class="m-auto p-10 text-center text-4xl sm:text-4xl md:text-4xl lg:text-5xl {idea.font} {idea.rotation} leading-10"
		>
			{idea.text}
		</h1>
	</div>
{/snippet}

{#snippet frameCorner(imgSrcKey: imgSrcKey)}
	<div class="size-14 flex-none border-1 border-black bg-white p-1 md:size-28">
		<img src={imgSrcs[imgSrcKey]} class="h-full w-full" />
	</div>
{/snippet}

{#snippet frameTop()}
	<div class="flex grow border-1 border-black bg-white p-2">
		<div class="h-full grow">
			<img src={imgSrcs[getImgSrcKey('horz')]} class="h-full" />
		</div>
		<div class="flex h-full w-14 shrink-0 md:w-28">
			<img src={imgSrcs[getImgSrcKey('center')]} class="m-auto h-full" />
		</div>
		<div class="h-full grow">
			<img src={imgSrcs[getImgSrcKey('horz')]} class="h-full -scale-x-100" />
		</div>
	</div>
{/snippet}

{#snippet frameBottom()}
	<div class="flex grow border-1 border-black bg-white p-2">
		<div class="h-full grow">
			<img src={imgSrcs[getImgSrcKey('horz')]} class="h-full -scale-y-100" />
		</div>
		<div class="flex h-full w-14 shrink-0 md:w-28">
			<img src={imgSrcs[getImgSrcKey('center')]} class="m-auto h-full -scale-y-100" />
		</div>
		<div class="h-full grow">
			<img src={imgSrcs[getImgSrcKey('horz')]} class="h-full -scale-x-100 -scale-y-100" />
		</div>
	</div>
{/snippet}

{#snippet frameLeft()}
	<div class="h-full w-14 flex-none border-1 border-black bg-white md:w-28">
		<div class="h-full w-full p-2">
			<img src={imgSrcs[getImgSrcKey('vert')]} class="h-full w-full" />
		</div>
	</div>
{/snippet}

{#snippet frameRight()}
	<div class="h-full w-14 flex-none border-1 border-black bg-white md:w-28">
		<div class="h-full w-full p-2">
			<img src={imgSrcs[getImgSrcKey('vert')]} class="h-full w-full -scale-x-100" />
		</div>
	</div>
{/snippet}

<div
	class="flex min-h-screen flex-col items-center justify-center {$backgroundColor} transition duration-500"
>
	<div
		id="frame"
		class="mt-24 flex h-full max-h-[80vh] w-full max-w-[90vw] grow flex-col bg-black shadow-xl/20 ring-1 ring-black sm:max-w-[80vw] md:max-w-180 lg:max-w-225"
	>
		<div class="flex h-14 w-full md:h-28">
			{@render frameCorner($frameCornerImgSrcs.tl)}
			{@render frameTop()}
			{@render frameCorner($frameCornerImgSrcs.tr)}
		</div>
		<div class="flex size-28 w-full grow">
			{@render frameLeft()}
			{@render frameCenter()}
			{@render frameRight()}
		</div>
		<div class="flex h-14 w-full md:h-28">
			{@render frameCorner($frameCornerImgSrcs.bl)}
			{@render frameBottom()}
			{@render frameCorner($frameCornerImgSrcs.br)}
		</div>
	</div>
	<div class="mt-6 mb-6 flex gap-4">
		<button
			id="randomize-button"
			class="group h-12 w-12 rounded-full border-2 border-black bg-gray-400/50 p-2 opacity-70 shadow-md/40 transition duration-200 hover:scale-130 hover:bg-white/80 hover:opacity-100"
			on:click={newIdea}
		>
			<img src={randomizeIcon} class="transition duration-200 group-hover:scale-95" />
		</button>
	</div>
</div>

<div class="absolute top-0 left-0 -z-50">
	<p class="font-0">a</p>
	<p class="font-1">a</p>
	<p class="font-2">a</p>
	<p class="font-3">a</p>
	<p class="font-4">a</p>
	<p class="font-5">a</p>
	<p class="font-6">a</p>
	<p class="font-7">a</p>
	<p class="font-8">a</p>
	<p class="font-9">a</p>
	<p class="font-10">a</p>
</div>
