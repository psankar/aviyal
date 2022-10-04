<script lang="ts">
	export let letter: string;
	export let disabled: boolean;
	export let btnColor: string;
</script>

<button class="tile" style="background: {btnColor}" {disabled}>
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
