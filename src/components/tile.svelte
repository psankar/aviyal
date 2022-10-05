<script lang="ts">
	import { createEventDispatcher } from 'svelte';

	export let letter: string;
	export let disabled: boolean;
	export let btnColor: string;
	export let curX: number;
	export let curY: number;
	export let cb: any;
</script>

<button class="tile" style="background: {btnColor}" {disabled} on:click={() => cb(curX, curY)}>
	{letter}
</button>

<style>
	.tile {
		color: white;
		font-family: monospace;
		width: 108px;
		height: 108px;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 40px;
	}
</style>
