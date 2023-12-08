<script lang="ts">
	import { onDestroy, onMount } from 'svelte';

	export let query = 'section';
	export let attribute = 'data-index';
	export let offset = 0.5;
	export let onStepEnter = (i: number, direction: string) => {};
	export let onStepExit = (i: number, direction: string) => {};
	export let debug = false;

	let foreground: HTMLDivElement;
	let observer: IntersectionObserver;

	let mounted = false;
	onMount(() => (mounted = true));

	onDestroy(destroyObserver);

	$: if (mounted) makeObserver(`-${offset * 100}% 0px -${100 - offset * 100}% 0px`, query);

	function makeObserver(rootMargin: string, query: string) {
		destroyObserver();

		observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					const index = +(entry.target.getAttribute(attribute) || '');
					const direction = entry.isIntersecting
						? entry.boundingClientRect.y < 0
							? 'up'
							: 'down'
						: entry.boundingClientRect.y > 0
						? 'up'
						: 'down';
					if (debug && !entry.isIntersecting) console.log(index, entry.isIntersecting, direction);
					if (entry.isIntersecting) onStepEnter(index, direction);
					else onStepExit(index, direction);
				});
			},
			{ rootMargin }
		);

		foreground.querySelectorAll(query).forEach((element) => observer.observe(element));
	}

	function destroyObserver() {
		if (observer) observer.disconnect();
	}
</script>

<div bind:this={foreground}>
	<slot />
</div>
