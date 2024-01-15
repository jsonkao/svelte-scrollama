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

		/** @type {Object.<string, number>} */
		const previousY = {};

		observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					const index = entry.target.getAttribute(attribute) || '';
					const {
						boundingClientRect: { y },
						isIntersecting
					} = entry;

					if (debug)
						console.log({
							index,
							y,
							isIntersecting,
							previousY: previousY[index]
						});

					if (isIntersecting) {
						if (y < previousY[index]) {
							// Scrolling down enter
							onStepEnter({ index, direction: 'down' });
						} else {
							// Scrolling up enter
							onStepEnter({ index, direction: 'up' });
						}
					} else {
						if (y > previousY[index]) {
							// Scrolling up leave
							onStepExit({ index, direction: 'up' });
						} else {
							// Scrolling down leave
							onStepExit({ index, direction: 'down' });
						}
					}

					previousY[index] = y;
				});
			},
			{ rootMargin, threshold: [0, 1] }
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
