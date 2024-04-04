<script lang="ts">
    import { onMount } from "svelte";

    let width = 600;
    let height = 300;
    export let color = "#333";
    export let background = "#fff";

    let canvas;
    let context;
    let isDrawing;
    let start;
    let coords = [];

    let t, l;

    onMount(() => {
        context = canvas.getContext("2d");
        context.lineWidth = 4;

        handleSize();
    });

    const handleStart = ({ offsetX: x, offsetY: y }) => {
        isDrawing = true;
        start = { x, y };
    };

    const handleEnd = () => {
        isDrawing = false;
    };

    const handleMove = ({ offsetX: x1, offsetY: y1 }) => {
        if (!isDrawing) return;

        context.beginPath();
        context.moveTo(start.x, start.y);
        context.lineTo(x1, y1);
        context.closePath();
        context.stroke();
        start = { x: x1, y: y1 };
        coords.push(start);
        console.log(`${coords}`);
    };

    const handleSize = () => {
        const { top, left } = canvas.getBoundingClientRect();
        t = top;
        l = left;
    };
    const clear = () => {
        context.clearRect(0, 0, width, height);
    };

    const save = () => {
        // Convert coords array to CSV string
        let csvContent = "data:text/csv;charset=utf-8,";
        csvContent += "X,Y\n"; // Column headers
        coords.forEach((coord) => {
            csvContent += `${coord.x},${coord.y}\n`;
        });

        // Create a link and trigger download
        const encodedUri = encodeURI(csvContent);
        const link = document.createElement("a");
        link.setAttribute("href", encodedUri);
        link.setAttribute("download", "coords.csv");
        document.body.appendChild(link); // Required for Firefox

        link.click(); // This will download the file
        document.body.removeChild(link); // Clean up
    };
</script>

<svelte:window on:resize={handleSize} />
<div>
    <button on:click={clear} class="clear"> clear </button>
    <button on:click={save}> save </button>
</div>
<div>
    <canvas
        {width}
        {height}
        style:background
        bind:this={canvas}
        on:mousedown={handleStart}
        on:touchstart={(e) => {
            const { clientX, clientY } = e.touches[0];
            handleStart({
                offsetX: clientX - l,
                offsetY: clientY - t,
            });
        }}
        on:mouseup={handleEnd}
        on:touchend={handleEnd}
        on:mouseleave={handleEnd}
        on:mousemove={handleMove}
        on:touchmove={(e) => {
            const { clientX, clientY } = e.touches[0];
            handleMove({
                offsetX: clientX - l,
                offsetY: clientY - t,
            });
        }}
    />
</div>
{coords}

<style>
    canvas {
        border-radius: 15px;
    }
    button {
        border-radius: 10px;
        font-size: 20pt;
        background-color: black;
        border: none;
        color: green;
        padding: 15px 32px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-family: "Spectral", serif;
    }
    .clear {
        color: red;
    }
</style>
