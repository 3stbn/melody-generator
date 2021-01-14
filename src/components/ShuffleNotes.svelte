<script>
    import _ from "lodash";

    export let notes = [];
    export let sequence = [];
    export let shuffledNotes = [];
    let shuffleWeights = {
        1: [50, 30, 10, 10],
        2: [50, 15, 15, 15],
        3: [40, 20, 10, 20],
    };
    function shuffleNotes(weight, notes, sequence) {
        const weightedNotes = _.shuffle(notes).reduce((acc, curr, i) => {
            acc = [...acc, ...Array(weight[i]).fill(curr)];
            return acc;
        }, []);
        const notesInSequence = sequence.filter((p) => p === "x").length;
        shuffledNotes = _.sampleSize(weightedNotes, notesInSequence).map((n) =>
            n.toLowerCase()
        );
    }
</script>

<style>
    .panel {
        position: absolute;
        top: 0;
        left: 0;
    }
    .item {
        width: 80px;
        height: 80px;
        background: rgb(32, 71, 102);
        border-bottom: 1px solid white;
        color: white;
        cursor: pointer;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    svg {
        width: 30px;
        height: 30px;
        fill: white;
    }
</style>

<div class="panel">
    {#each Object.entries(shuffleWeights) as [shuffle, shuffleWeight]}
        <div
            class="item"
            on:click={() => shuffleNotes(shuffleWeight, notes, sequence)}>
            <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="currentColor">
                <path
                    fill-rule="evenodd"
                    d="M4 2a1 1 0 011 1v2.101a7.002 7.002 0 0111.601 2.566 1 1 0 11-1.885.666A5.002 5.002 0 005.999 7H9a1 1 0 010 2H4a1 1 0 01-1-1V3a1 1 0 011-1zm.008 9.057a1 1 0 011.276.61A5.002 5.002 0 0014.001 13H11a1 1 0 110-2h5a1 1 0 011 1v5a1 1 0 11-2 0v-2.101a7.002 7.002 0 01-11.601-2.566 1 1 0 01.61-1.276z"
                    clip-rule="evenodd" />
            </svg>
            <span> {shuffle} </span>
        </div>
    {/each}
</div>
