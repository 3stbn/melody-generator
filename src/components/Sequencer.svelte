<script>
    export let sequence = [];
    export let shuffledNotes = [];

    $: notesMappedToSequence = mapNotesToSequence(sequence, shuffledNotes);

    function handleStep(step, i) {
        const value = step === "x" ? "-" : "x";
        sequence = [...sequence.slice(0, i), value, ...sequence.slice(i + 1)];
    }
    function mapNotesToSequence(sequence, shuffledNotes) {
        let noteindex = 0;
        return sequence.map((s) => {
            if (s === "x" && shuffledNotes.length >= 4) {
                noteindex++;
                return shuffledNotes[noteindex - 1];
            } else {
                return "";
            }
        });
    }
</script>

<style>
    .sequencer {
        display: grid;
        grid-template-columns: repeat(16, 1fr);
        column-gap: 3px;
    }
    .step {
        background-color: whitesmoke;
        cursor: pointer;
        display: flex;
        justify-content: center;
        align-items: center;
        color: white;
        text-transform: uppercase;
    }
    .active {
        background-color: steelblue;
    }
</style>

<div class="sequencer">
    {#each sequence as step, i}
        <div
            class="step"
            class:active={step === 'x'}
            on:mousedown={() => handleStep(step, i)}>
            <span> {notesMappedToSequence[i]} </span>
        </div>
    {/each}
</div>
