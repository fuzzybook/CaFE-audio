<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';
	import Arc from './Arc.svelte';
	import { getContext } from 'svelte';

	export let series = [];
	export let thickness = 5;
	export let width = null;
	export let height = null;
	export let textSize = null;
	export let showProgressValue = true;

	const ts = new Date().getTime();
	const maskId = 'tx_mask_' + ts + Math.floor(Math.random() * 999);
	const valStore = getContext('valStore');

	if(width == null)
		//Default width when not specified
		width = 75;

	if(height == null)
		//Default height when not specified
		height = 100;

	if(textSize == null)
		textSize = 100;

	const twOpts = {
		duration: 1000,
		easing: cubicOut
	};

	const maskSerie = {
		offset: tweened(100, twOpts),
		prevOffset: tweened(0, twOpts),
		color: '#fff'
	};

	$: {
		maskSerie.prevOffset.set(series.reduce((a, s) => a + s.perc < 100 ? a + s.perc : 100, 0));
	}


</script>

<style>
	.progress-bg {
		fill: #f1f1f1;
	}

	.progressbar-radial .progress-bg {
		fill: #fff;
		stroke-width: 1;
		/* stroke: #f1f1f1; */
		stroke: #ddd;
	}

	.progress-value{
		font-family: "Courier New", Courier, monospace;
		font-weight: bold;
	}

	.progress-value-inverted {
		fill: #fff;
	}

</style>

<svg class="progressbar progressbar-radial" viewBox="0 0 100 {height}" width="{width}" xmlns="http://www.w3.org/2000/svg">
	{#if showProgressValue}
		<defs>
			<mask id="{maskId}" x="0" y="0" width="100" height="100%">
				<Arc {...maskSerie} {thickness} />
				<circle cx="50" cy="50" r="{50 - thickness}" fill="#fff" />
			</mask>
		</defs>
	{/if}

	<circle class="progress-bg" cx="50" cy="50" r="49"/>
	{#each series as serie}
		<Arc offset = {serie.offset} prevOffset = {serie.prevOffset} color = {serie.color}  {thickness} />
	{/each}
	{#if showProgressValue}
		<text class="progress-value progress-value-inverted" text-anchor="middle" dominant-baseline="central" x="50%" y="50%" font-size="{textSize}%">{$valStore}</text>
		<text mask="url(#{maskId})"  class="progress-value" text-anchor="middle" dominant-baseline="central" x="50%" y="50%" font-size="{textSize}%">{$valStore}</text>
	{/if}
</svg>