<script lang="ts">
	import { onMount } from 'svelte';
	import Tile from './tile.svelte';

	let state: any[];
	let blankX, blankY: number;

	onMount(async () => {
		await new Promise((resolve) => setTimeout(resolve, 2000));

		const inputStr = [
			['h', 'w', 's', 'b', 'g'],
			['a', 'e', 'o', 'w', 'o'],
			['m', 'l', 'r', 'e', 'a'],
			['e', 'l', 'l', 'e', 'r'],
			['o', 'd', 't', 'd', ' ']
		];

		const gridSize = inputStr.length;
		blankX = gridSize - 1;
		blankY = gridSize - 1;

		state = [];

		for (let i = 0; i < gridSize; i++) {
			let row = [];

			for (let j = 0; j < inputStr[i].length; j++) {
				let disabled = true;
				let btnColor = 'lightseagreen';
				if (
					(i === blankX && (j === blankY - 1 || j === blankY + 1)) ||
					(j === blankY && (i === blankX - 1 || i === blankX + 1))
				) {
					disabled = false;
					btnColor = 'purple';
				}

				row.push({ letter: inputStr[i][j], disabled: disabled, btnColor: btnColor });
			}
			state.push(row);
		}
	});
</script>

{#if state}
	<table class="board">
		{#each state as i}
			<tr>
				{#each i as j}
					<td>
						<Tile letter={j.letter} disabled={j.disabled} btnColor={j.btnColor} />
					</td>
				{/each}
			</tr>
		{/each}
	</table>
{:else}
	<div class="loading" />
{/if}

<style>
	.board {
		border: 16px solid lightblue;
		border-radius: 10px;
	}

	.loading {
		border: 16px solid darkgray;
		border-top: 16px solid purple;
		border-bottom: 16px solid purple;
		border-radius: 50%;
		width: 120px;
		height: 120px;
		animation: spin 2s linear infinite;
	}

	@keyframes spin {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
</style>
