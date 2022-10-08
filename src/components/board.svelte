<script lang="ts">
	import { onMount } from 'svelte';
	import Tile, { blankTile, clickableTile, defaultTile } from './tile.svelte';
	import sha256 from 'crypto-js/sha256';

	let state: any[];
	let blankX: number, blankY: number;
	let gridSize: number;
	let wantHashes: string[];
	let err: string;
	let matched: boolean[];
	let puzzleSolved: boolean;

	async function getResponse() {}

	onMount(async () => {
		const response = await fetch(
			'https://gist.githubusercontent.com/psankar/0cd1d54a963abffbaaff7534fc433246/raw/e1a435fc37bf00f8e0a395bcb6896e521239c3af/aviyal.json',
			{
				method: 'GET'
			}
		);

		if (!response.ok) {
			err = `HTTP Error ${response.status}`;
			return;
		}

		const fetchedData = await response.json();

		wantHashes = fetchedData.wantHashes;
		gridSize = fetchedData.inputStr.length;
		blankX = gridSize - 1;
		blankY = gridSize - 1;

		state = [];
		matched = [];
		puzzleSolved = false;

		for (let i = 0; i < gridSize; i++) {
			let row = [];

			for (let j = 0; j < fetchedData.inputStr[i].length; j++) {
				if (
					(i === blankX && (j === blankY - 1 || j === blankY + 1)) ||
					(j === blankY && (i === blankX - 1 || i === blankX + 1))
				) {
					row.push({ letter: fetchedData.inputStr[i][j], tileType: clickableTile });
				} else if (i == blankX && j == blankY) {
					row.push({ letter: fetchedData.inputStr[i][j], tileType: blankTile });
				} else {
					row.push({ letter: fetchedData.inputStr[i][j], tileType: defaultTile });
				}
			}
			matched.push(false);
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

		let solved: boolean = true;

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

				if (newState[i][j].letter !== ' ') {
					word += newState[i][j].letter;
				}
			}

			const hash = sha256(word).toString();

			if (wantHashes.includes(hash)) {
				matched[i] = true;
			} else {
				solved = false;
				matched[i] = false;
			}
		}
		state = newState;

		if (solved) {
			puzzleSolved = true;
		}
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

{#if err}
	<h1>Error getting game data: {err}</h1>
{:else if state}
	<table class="board">
		{#each state as i, curX}
			<tr>
				{#each i as j, curY}
					<td>
						<Tile letter={j.letter} tileType={j.tileType} {curX} {curY} cb={recalculate} />
					</td>
				{/each}
				<td>{matched[curX] ? '✔️' : ''} </td>
			</tr>
		{/each}
	</table>
	{#if puzzleSolved}
		<h1 class="h1">Congrats! You have correctly found all the words!!!</h1>
	{/if}
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

	.h1 {
		text-align: center;
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
