<script>
	import { onDestroy, onMount } from 'svelte';

	export let selector = 'section';
	export let attribute = 'data-index';
	export let offset = 0.5;
	export let debug = false;

	/**
	 * @param {{index: any, direction: "up" | "down"}} obj An object containing the index of the entered step and the direction of the scroll
	 */
	export let onStepEnter = (obj) => {};
	/**
	 * @param {{index: any, direction: "up" | "down"}} obj An object containing the index of the exited step and the direction of the scroll
	 */
	export let onStepExit = (obj) => {};

	/** @type {HTMLDivElement} */
	let foreground;
	/** @type {IntersectionObserver} */
	let observer;

	let mounted = false;
	onMount(() => (mounted = true));
	onDestroy(destroyObserver);

	$: if (mounted) makeObserver(`-${offset * 100}% 0px -${100 - offset * 100}% 0px`, selector);

	/**
	 * @param {string} rootMargin The root margin calculated from the offset
	 * @param {string} selector The selector used to find the steps
	 */
	function makeObserver(rootMargin, selector) {
		destroyObserver();

		observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					const index = entry.target.getAttribute(attribute);
					const direction = entry.isIntersecting
						? entry.boundingClientRect.y < 0
							? 'up'
							: 'down'
						: entry.boundingClientRect.y > 0
						? 'up'
						: 'down';
					if (debug) console.log(index, entry.isIntersecting, direction);
					if (entry.isIntersecting) onStepEnter({ index, direction });
					else onStepExit({ index, direction });
				});
			},
			{ rootMargin }
		);

		foreground.querySelectorAll(selector).forEach((element) => observer.observe(element));
	}

	function destroyObserver() {
		if (observer) observer.disconnect();
	}
</script>

<div bind:this={foreground}>
	<slot />
</div>
