<script>

    let username;
    let canvas;

    

    function handleClick() {
        const ctx = canvas.getContext("2d");

        // Set the background color
        ctx.fillStyle = "#ffffff";
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        // Function to generate random curves
        function drawRandomCurve() {
            ctx.beginPath();
            ctx.strokeStyle = getRandomColor();
            ctx.lineWidth = 10;
            ctx.moveTo(getRandomNumber(0, canvas.width), getRandomNumber(0, canvas.height));
            ctx.bezierCurveTo(
                getRandomNumber(0, canvas.width), getRandomNumber(0, canvas.height),
                getRandomNumber(0, canvas.width), getRandomNumber(0, canvas.height),
                getRandomNumber(0, canvas.width), getRandomNumber(0, canvas.height)
            );
            ctx.stroke();
        }

        // Function to generate a random number within a range
        function getRandomNumber(min, max) {
            return Math.random() * (max - min) + min;
        }

        // Function to generate a random color
        function getRandomColor() {
            const letters = "0123456789ABCDEF";
            let color = "#";
            for (let i = 0; i < 6; i++) {
                color += letters[Math.floor(Math.random() * 16)];
            }
            return color;
        }

        // Draw a specified number of random curves
        const numCurves = 3;
        for (let i = 0; i < numCurves; i++) {
            drawRandomCurve();
        }
    }

</script>


<div class="flex flex-col w-full h-screen justify-center items-center space-y-2">

    <canvas id="randomCurvesCanvas" width="96" height="96" bind:this={canvas}></canvas>


    <form class="flex flex-col w-full items-center space-y-2">
        <input class="rounded-md w-48 h-8 border-neutral-200 border-2 p-2" type="text" placeholder="Enter username" bind:value={username}>
        <button class="px-4 py-1 bg-blue-300 rounded-md" on:click={handleClick}>
            Create Identicon
        </button>
    </form>

</div>
