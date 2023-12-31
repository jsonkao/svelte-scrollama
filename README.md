Continuing the [Scrollama](https://github.com/russellsamora/scrollama) [legacy](https://github.com/jsonkao/react-scrollama). An alternative to [svelte-scroller](https://github.com/sveltejs/svelte-scroller) that bypasses scroll event listeners and uses `position: sticky`.

## Usage

Import the `Stepper.svelte` component. Then, use like so:

```svelte
<Stepper {onStepEnter} {onStepExit}>
    {#each paragraphs as text, index}
        <section data-index={index}>{text}</section>
    {/each}
</Stepper>
```

## Props

```js
export let query = 'section'; // The DOM nodes to look for inside the stepper
export let attribute = 'data-index'; // Hacky way to track the index. Should be improved
export let offset = 0.5; // Offset
export let onStepEnter = (i: number, direction: string) => {}; // Step enter callback
export let onStepExit = (i: number, direction: string) => {}; // Step exit callback
export let debug = false; // Whether to console.log step enters and exits
```
