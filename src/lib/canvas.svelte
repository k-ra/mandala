<script lang="ts">
    // Import the functions you need from the SDKs you need
    import { initializeApp } from "firebase/app";
    import { getDatabase, ref, set } from "firebase/database";
    const firebaseConfig = {
        apiKey: "AIzaSyClAN9gW5A_catXwLyFIf-IIedp-zue70o",
        authDomain: "mandala-2dfea.firebaseapp.com",
        databaseURL: "https://mandala-2dfea-default-rtdb.firebaseio.com",
        projectId: "mandala-2dfea",
        storageBucket: "mandala-2dfea.appspot.com",
        messagingSenderId: "693026432935",
        appId: "1:693026432935:web:31c8a5d9b0c6196a117882",
    };

    const app = initializeApp(firebaseConfig);
    const database = getDatabase(app);

    import { onMount } from "svelte";

    let width = 1000;
    let height = 500;

    let canvas;
    let context;
    let isDrawing;
    let start;
    let coords = [];
    let drawnyet = false;
    let message = "";
    let t, l;

    onMount(() => {
        context = canvas.getContext("2d");
        context.lineWidth = 4;
        handleSize();
    });

    const handleStart = ({ offsetX: x, offsetY: y }) => {
        if (!drawnyet) {
            isDrawing = true;
            drawnyet = true;
            start = { x, y };
            message = "";
        }
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
        drawnyet = false;
    };

    const save = () => {
        const coordsRef = ref(database, "COORDS"); // Reference to a node where you want to store the coordinates
        set(coordsRef, coords)
            .then(() => {
                console.log("Coordinates saved successfully!");
                message = "sent!";
                clear();
            })
            .catch((error) => {
                console.error("Error saving coordinates: ", error);
                message = "Error!!";
            });
    };
</script>

<svelte:window on:resize={handleSize} />

<div class="canvaswrap">
    <button on:click={clear} class="clear"> redo </button>

    <canvas
        {width}
        {height}
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
    <button on:click={save}> save </button>
</div>
<h2>{message}</h2>

<style>
    canvas {
        border-radius: 15px;
        background: #fff;
    }
    h2 {
        text-align: center;
        color: #ffb6c1;
    }
    button {
        border-radius: 10px;
        font-size: 20pt;
        background-color: rgb(70, 70, 70);
        border: none;
        color: rgb(76, 235, 76);
        padding: 15px 32px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-family: "Spectral", serif;
    }
    .clear {
        color: rgb(255, 79, 79);
    }
    .canvaswrap {
        display: flex;
        justify-content: center;
        align-items: center;
    }
</style>
