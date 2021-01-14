<script>
    import { onDestroy, onMount } from "svelte";
    import { synth } from "../instruments";
    import { notesNames } from "../utils";
    import _ from "lodash";
    export let notes = [];
    let piano;

    function generatePianoMap(startOctave, endOctave) {
        const allRange = _.range(startOctave, endOctave)
            .map((range) => notesNames.map((note) => note + range))
            .flat();
        return allRange.reduce((obj, curr) => {
            obj[curr] = (curr.includes("#") && "black") || "white";
            return obj;
        }, {});
    }

    function handlePress(event, note) {
        synth.triggerAttackRelease(note, "4n");
        notes = [...notes, note];
    }
    onDestroy(() => {
        piano && piano.destroy();
    });
</script>

<style>
    * {
        padding: 0;
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
    } 

    .page {
        text-align: center;
    }

    .table {
        position: absolute;
        top: 0;
        bottom: 0;
        right: 0;
        left: 0;
        margin: auto;
        width: -webkit-fit-content;
        width: -moz-fit-content;
        width: fit-content;
        height: 300px;
        padding: 0 50px;
        background: #4e4e4e;
        border-radius: 10px;
        overflow: hidden;
        background: -webkit-gradient(
            linear,
            left top,
            left bottom,
            from(#5f5d5a),
            color-stop(25%, #424242),
            to(#18181a)
        );
        background: linear-gradient(#5f5d5a, #424242 25%, #18181a);
        -webkit-box-shadow: 0 3px 5px #575656,
            inset 0px 0px 0px 1px rgba(220, 220, 220, 0.2);
        box-shadow: 0 3px 5px #575656,
            inset 0px 0px 0px 1px rgba(220, 220, 220, 0.2);
    }

    ul {
        list-style: none;
        display: inline-block;
        margin-top: 60px;
    }

    ul li {
        user-select: none;
        background: whitesmoke;
        padding: 0;
        margin: 0;
        display: inline-block;
        border: 1px solid #ccc;
        padding: 180px 22px 50px;
        border-radius: 0 0 5px 5px;
        -webkit-box-shadow: 0 2px 2px #aaa;
        box-shadow: 0 2px 2px #aaa;
        position: relative;
        z-index: 1;
        cursor: pointer;
    }

    ul li:hover {
        color: #c0c1bd;
    }

    ul li.black {
        z-index: 2;
        position: absolute;
        width: 32px;
        top: 60px;
        margin-left: -16px;
        padding: 140px 0px 10px;
        border-radius: 0 0 1px 1px;
        border-width: 0 6px 8px;
        border-color: #161616;
        border-bottom-color: #606161;
        background: -webkit-gradient(
            linear,
            left top,
            left bottom,
            from(#282829),
            color-stop(25%, #151b19),
            to(#5f5d5d)
        );
        background: linear-gradient(#282829, #151b19 25%, #5f5d5d);
        -webkit-box-shadow: 0 3px 5px #ccc,
            inset 0px 0px 0px 1px rgba(220, 220, 220, 0.2);
        box-shadow: 0 3px 5px #ccc,
            inset 0px 0px 0px 1px rgba(220, 220, 220, 0.2);
    }

    ul li.black:active {
        -webkit-box-shadow: -1px -1px 2px rgba(255, 255, 255, 0.2) outset,
            0 -2px 2px 3px rgba(24, 23, 23, 0.6) inset,
            0 1px 2px rgba(0, 0, 0, 0.5);
        box-shadow: -1px -1px 2px rgba(255, 255, 255, 0.2) outset,
            0 -2px 2px 3px rgba(24, 23, 23, 0.6) inset,
            0 1px 2px rgba(0, 0, 0, 0.5);
        background: -webkit-gradient(
            linear,
            left top,
            right top,
            from(#444),
            to(#151b19)
        );
        background: linear-gradient(to right, #444 0%, #151b19 100%);
        border-color: #313131;
        border-bottom-color: #414242;
    }

    ul li.white:active {
        border-top: 1px solid #777;
        border-left: 1px solid #999;
        border-bottom: 1px solid #999;
        -webkit-box-shadow: 2px 0 3px rgba(0, 0, 0, 0.1) inset,
            -5px 5px 20px rgba(0, 0, 0, 0.2) inset, 0 0 3px rgba(0, 0, 0, 0.2);
        box-shadow: 2px 0 3px rgba(0, 0, 0, 0.1) inset,
            -5px 5px 20px rgba(0, 0, 0, 0.2) inset, 0 0 3px rgba(0, 0, 0, 0.2);
        background: -webkit-gradient(
            linear,
            left top,
            left bottom,
            from(#fff),
            to(#e9e9e9)
        );
        background: linear-gradient(to bottom, #fff 0%, #e9e9e9 100%);
    }
</style>

<div class="page">
    <div class="table">
        <ul>
            {#each Object.entries(generatePianoMap(3, 5)) as [note, color]}
                <li class={color} on:mousedown={(e) => handlePress(e, note)} />
            {/each}
        </ul>
    </div>
</div>
