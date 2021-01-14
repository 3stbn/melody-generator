<script>
    import { midi, clip } from "scribbletune";
    import { v4 as uuidv4 } from "uuid";
    import { fade, scale } from "svelte/transition";
    import axios from "axios";

    export let shuffledNotes;
    export let notes;
    export let sequence;
    export let subdiv;

    let copied = false;
    let shareableLink = "";
    let showToast = false;

    let inputRef;

    $: midiResult = generateMidi(shuffledNotes, sequence, subdiv);

    function generateMidi(notes, sequence, subdiv) {
        const c = clip({ notes, sequence, subdiv });
        const midiAnchor = midi(c);
        return [midiAnchor.href, midiAnchor.download];
    }

    function copyLink() {
        inputRef.select();
        inputRef.setSelectionRange(0, 99999); /* For mobile devices */

        /* Copy the text inside the text field */
        document.execCommand("copy");
        copied = true;
    }

    function closeToast() {
        showToast = false;
        shareableLink = "";
        copied = false;
    }
    function openToast() {
        showToast = true;
    }
    async function generateSharableLink(
        shuffledNotes,
        notes,
        sequence,
        subdiv
    ) {
        const id = uuidv4();
        try {
            const clipToSave = { notes, shuffledNotes, sequence, subdiv, id };
            await axios.post(
                `https://green-fire-c050.stban.workers.dev/melody/${id}`,
                clipToSave
            );
            shareableLink = `${window.location.protocol}//${window.location.host}/?melodyId=${id}`;
            openToast();
        } catch (error) {
            console.log(error);
            shareableLink = "";
        }
    }
</script>

<style>
    .actions {
        position: absolute;
        display: flex;
        flex-direction: column;
        align-items: flex-end;
        top: 10px;
        right: 10px;
        text-align: right;
        z-index: 99;
    }
    svg {
        margin-top: 20px;
        width: 40px;
        height: 40px;
        display: block;
        fill: rgb(32, 71, 102);
        cursor: pointer;
    }
    .sharableLinkToast {
        display: flex;
        background-color: rgb(32, 71, 102);
        padding: 1rem 1rem;
    }
    input {
        width: 200px;
    }
    .sharableLinkToast svg {
        width: 35px;
        height: 35px;
        fill: none;
        stroke: white;
        padding: 0;
        margin: 0;
    }
</style>

<div class="actions">
    <a href={midiResult[0]} download={midiResult[1]}>
        <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 20 20"
            fill="currentColor">
            <path
                fill-rule="evenodd"
                d="M3 17a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1zm3.293-7.707a1 1 0 011.414 0L9 10.586V3a1 1 0 112 0v7.586l1.293-1.293a1 1 0 111.414 1.414l-3 3a1 1 0 01-1.414 0l-3-3a1 1 0 010-1.414z"
                clip-rule="evenodd" />
        </svg>
    </a>

    <svg
        on:click={() => generateSharableLink(shuffledNotes, notes, sequence, subdiv)}
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
        fill="currentColor">
        <path
            d="M15 8a3 3 0 10-2.977-2.63l-4.94 2.47a3 3 0 100 4.319l4.94 2.47a3 3 0 10.895-1.789l-4.94-2.47a3.027 3.027 0 000-.74l4.94-2.47C13.456 7.68 14.19 8 15 8z" />
    </svg>
    {#if showToast}
        <div class="sharableLinkToast" transition:fade>
            <input
                type="text"
                value={shareableLink}
                readonly
                bind:this={inputRef} />
            {#if !copied}
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 24 24"
                    on:click={copyLink}>
                    <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3" />
                </svg>
            {:else}
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                    on:click={copyLink}
                    transition:scale
                    fill="currentColor">
                    <path d="M9 2a1 1 0 000 2h2a1 1 0 100-2H9z" />
                    <path
                        fill-rule="evenodd"
                        d="M4 5a2 2 0 012-2 3 3 0 003 3h2a3 3 0 003-3 2 2 0 012 2v11a2 2 0 01-2 2H6a2 2 0 01-2-2V5zm9.707 5.707a1 1 0 00-1.414-1.414L9 12.586l-1.293-1.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"
                        clip-rule="evenodd" />
                </svg>
            {/if}
            <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                on:click={closeToast}
                stroke="currentColor">
                <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
        </div>
    {/if}
</div>
