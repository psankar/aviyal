<script context="module">
	export const defaultTile = 'default-tile';
	export const clickableTile = 'clickable-tile';
	export const blankTile = 'blank-tile';
</script>

<script lang="ts">
	export let letter: string;
	export let tileType: string;
	export let curX: number;
	export let curY: number;
	export let cb: any;
</script>

<button class={tileType} disabled={tileType !== clickableTile} on:click={() => cb(curX, curY)}>
	{letter}
</button>

<style>
	.default-tile {
		color: white;
		background: purple;
		font-family: monospace;
		width: 108px;
		height: 108px;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 40px;
	}
	.clickable-tile {
		color: white;
		background: purple;
		font-family: monospace;
		width: 108px;
		height: 108px;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 40px;
		cursor: pointer;
	}
	.clickable-tile:hover {
		-ms-transform: scale(1.1); /* IE 9 */
		-webkit-transform: scale(1.1); /* Safari 3-8 */
		transform: scale(1.1);
	}
	.blank-tile {
		color: white;
		background: purple;
		font-family: monospace;
		width: 108px;
		height: 108px;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 40px;
	}
</style>
