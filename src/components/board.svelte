<script lang="ts">
	import { onMount } from 'svelte';
	import Tile, { blankTile, clickableTile, defaultTile } from './tile.svelte';
	import sha256 from 'crypto-js/sha256';

	let state: any[];
	let blankX: number, blankY: number;
	let gridSize: number;
	let wantHashes: string[];

	onMount(async () => {
		await new Promise((resolve) => setTimeout(resolve, 2000));

		const inputStr = [
			['h', 'w', 's', 'b', 'g'],
			['a', 'e', 'o', 'w', 'o'],
			['m', 'l', 'r', 'e', 'a'],
			['e', 'l', 'l', 'e', 'r'],
			['o', 'd', 't', 'd', ' ']
		];

		gridSize = inputStr.length;
		blankX = gridSize - 1;
		blankY = gridSize - 1;

		state = [];

		for (let i = 0; i < gridSize; i++) {
			let row = [];

			for (let j = 0; j < inputStr[i].length; j++) {
				if (
					(i === blankX && (j === blankY - 1 || j === blankY + 1)) ||
					(j === blankY && (i === blankX - 1 || i === blankX + 1))
				) {
					row.push({ letter: inputStr[i][j], tileType: clickableTile });
				} else if (i == blankX && j == blankY) {
					row.push({ letter: inputStr[i][j], tileType: blankTile });
				} else {
					row.push({ letter: inputStr[i][j], tileType: defaultTile });
				}
			}
			state.push(row);
		}
	});

	const recalculate = (curX: number, curY: number) => {
		if (curX < 0 || curY < 0 || curX >= gridSize || curY >= gridSize) {
			return;
		}

		let newState = structuredClone(state);
		newState[blankX][blankY] = newState[curX][curY];
		newState[curX][curY] = { letter: ' ', tileType: blankTile };

		blankX = curX;
		blankY = curY;

		for (let i: number = 0; i < newState.length; i++) {
			let word = '';
			for (let j: number = 0; j < newState[i].length; j++) {
				if (
					(i === blankX && (j === blankY - 1 || j === blankY + 1)) ||
					(j === blankY && (i === blankX - 1 || i === blankX + 1))
				) {
					newState[i][j].tileType = clickableTile;
				} else if (i == blankX && j == blankY) {
					newState[i][j].tileType = blankTile;
				} else {
					newState[i][j].tileType = defaultTile;
				}
				word += newState[i][j].letter;
			}

			const hash = sha256(word);
			console.log(word, hash.toString());
		}
		state = newState;
	};

	function handleKeydown(event: KeyboardEvent) {
		switch (event.key) {
			case 'ArrowLeft':
				recalculate(blankX, blankY + 1);
				break;
			case 'ArrowUp':
				recalculate(blankX + 1, blankY);
				break;
			case 'ArrowRight':
				recalculate(blankX, blankY - 1);
				break;
			case 'ArrowDown':
				recalculate(blankX - 1, blankY);
				break;
		}
	}
</script>

<svelte:window on:keydown={handleKeydown} />

{#if state}
	<table class="board">
		{#each state as i, curX}
			<tr>
				{#each i as j, curY}
					<td>
						<Tile letter={j.letter} tileType={j.tileType} {curX} {curY} cb={recalculate} />
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
		margin-left: auto;
		margin-right: auto;
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
