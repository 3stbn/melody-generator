<script>
	import { clip } from "scribbletune";
	import axios from "axios";
	import Actions from "./components/Actions.svelte";
	import _ from "lodash";
	import Piano from "./components/Piano.svelte";
	import Sequencer from "./components/Sequencer.svelte";
	import NotesGrid from "./components/NotesGrid.svelte";

	import ShuffleNotes from "./components/ShuffleNotes.svelte";
	import { onMount } from "svelte";

	let notes = [];
	let shuffledNotes = [];
	let steps = 16;
	let sequence = Array(steps).fill("-");
	let hasStartedAudio = false;
	let playing = false;
	let subdiv = "8n";

	let bpm = 120;

	$: Tone.Transport.bpm.value = bpm;
	$: pattern = sequence.join("");

	let currClip;

	onMount(async () => {
		const urlParams = new URLSearchParams(window.location.search);
		const melodyId = urlParams.get("melodyId");
		if (melodyId) {
			try {
				const { data } = await axios.get(
					`https://green-fire-c050.stban.workers.dev/melody/${melodyId}`
				);
				notes = data.notes;
				sequence = data.sequence;
				subdiv = data.subdiv;
				shuffledNotes = data.shuffledNotes;
			} catch (error) {
				console.log(error);
			}
		}
	});

	$: readyShuffle =
		notes.length >= 4 && sequence.filter((s) => s === "x").length >= 4;

	$: ready = readyShuffle && shuffledNotes.length >= 4;

	$: if (notes || patter || playing) {
		currClip = clip({
			instrument: "Synth",
			pattern,
			notes: shuffledNotes,
			subdiv,
		});
	}

	$: if (notes.length < 4 || sequence.filter((s) => s === "x").length < 4) {
		shuffledNotes = [];
	}

	async function startStop() {
		if (!hasStartedAudio) {
			await Tone.start();
			hasStartedAudio = true;
		}

		if (playing) {
			currClip.stop();
			Tone.Transport.stop();
			playing = false;
		} else {
			playing = true;
			await Tone.context.resume();
			currClip.start();
			Tone.Transport.start();
		}
	}
</script>

<style>
	main {
		text-align: center;
		display: grid;
		grid-template-rows: 1fr 8fr 1fr;
		min-height: 100vh;
	}
	footer {
		display: grid;
		grid-template-columns: 80px 12fr;
	}
	button {
		background-color: rgb(32, 71, 102);
		border: none;
	}
	button svg {
		fill: white;
		cursor: pointer;
		width: 30px;
		height: 30px;
	}
</style>

<main>
	{#if ready}
		<Actions {shuffledNotes} {notes} {subdiv} {sequence} />
	{/if}

	{#if readyShuffle}
		<ShuffleNotes {notes} {sequence} bind:shuffledNotes />
	{/if}

	<NotesGrid bind:notes />
	<Piano bind:notes />

	<footer>
		<button on:click={startStop}>
			{#if !ready}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 20 20"
					fill="currentColor">
					<path
						fill-rule="evenodd"
						d="M10 18a8 8 0 100-16 8 8 0 000 16zM7 9a1 1 0 100-2 1 1 0 000 2zm7-1a1 1 0 11-2 0 1 1 0 012 0zm-.464 5.535a1 1 0 10-1.415-1.414 3 3 0 01-4.242 0 1 1 0 00-1.415 1.414 5 5 0 007.072 0z"
						clip-rule="evenodd" />
				</svg>
			{:else if playing}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 24 24"
					width="30px"
					height="30px">
					<path d="M0 0h24v24H0z" fill="none" />
					<path d="M6 6h12v12H6z" />
				</svg>
			{:else}
				<svg
					xmlns="http://www.w3.org/2000/svg"
					viewBox="0 0 24 24"
					width="30px"
					height="30px">
					<path d="M8 5v14l11-7z" />
					<path d="M0 0h24v24H0z" fill="none" />
				</svg>
			{/if}
		</button>
		<Sequencer bind:sequence {shuffledNotes} />
	</footer>
</main>
