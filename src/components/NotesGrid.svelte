<script>
    export let notes = [];
    function handleRemove(i) {
        notes = notes.filter((_, idx) => idx !== i);
    }
</script>

<style>
    .notes {
        margin-top: 3px;
        display: flex;
        align-self: center;
        justify-self: center;
        width: 100%;
        height: 100%;
        justify-content: center;
        column-gap: 3px;
    }
    .note {
        background-color: rgb(32, 71, 102);
        position: relative;
        font-size: large;
        display: flex;
        font-weight: bold;
        color: white;
        text-transform: uppercase;
        justify-content: center;
        align-items: center;
        width: 100px;
    }
    svg {
        fill: white;
        position: absolute;
        cursor: pointer;
        bottom: 2px;
        right: 3px;
        width: 15px;
        height: 15px;
    }
</style>

<div class="notes">
    {#each notes as note, i}
        <div class="note">
            <div>{note}</div>
            <svg
                on:click={() => handleRemove(i)}
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="currentColor">
                <path
                    fill-rule="evenodd"
                    d="M10 18a8 8 0 100-16 8 8 0 000 16zM7 9a1 1 0 000 2h6a1 1 0 100-2H7z"
                    clip-rule="evenodd" />
            </svg>
        </div>
    {/each}
</div>
