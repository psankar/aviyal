<script lang="ts">
	import Tile from './tile.svelte';
	export let tiles: string[];
</script>

{#if tiles}
	<table>
		<tr>
			{#each tiles as value}
				<Tile {value} />
			{/each}
		</tr>
	</table>
{:else}
	<p class="loading">loading...</p>
{/if}
