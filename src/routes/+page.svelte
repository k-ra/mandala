<script lang="ts">
    import Canvas from "$lib/canvas.svelte";
    import message from "$lib/canvas.svelte";
</script>

<body>
    <div class="center">
        <h1>M A N D A L A</h1>
    </div>

    <Canvas />
    <h2 class="center"><i>draw your starter line</i></h2>
</body>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Spectral:ital,wght@0,200;0,300;0,400;0,500;0,600;0,700;0,800;1,200;1,300;1,400;1,500;1,600;1,700;1,800&display=swap");
    h1 {
        font-family: "Spectral", serif;
        color: white;
        font-size: 50pt;
    }

    body {
        background-image: url(mandala.png);
        background-repeat: repeat;
        background-position: center;
        background-size: 300% 100%;
        margin: 0;
        padding: 0;
        min-height: 100vh;
        background: rgb(70, 70, 70);
    }
    .center {
        text-align: center;
        color: white;
        padding: 2%;
    }
    .canvaswrap {
        display: flex;
        justify-content: center;
        align-items: center;
    }
    * {
        font-family: "Spectral", serif;
    }
</style>
