<script>

    let username;
    /**
     * @type {HTMLCanvasElement}
     */
    let canvas;

    async function sha256ToFloat(str) {
        const utf8Encoder = new TextEncoder();
        const data = utf8Encoder.encode(str);

        return crypto.subtle.digest('SHA-256', data).then(buffer => {
            const hashArray = Array.from(new Uint8Array(buffer));
            const hashHex = hashArray.map(byte => byte.toString(16).padStart(2, '0')).join('');
            
            // Convert the hexadecimal hash to a decimal number
            const hashDecimal = parseInt(hashHex, 16);

            // Convert the decimal number to a floating-point number
            const hashFloat = hashDecimal / Math.pow(10, hashHex.length);

            return hashFloat;
        });
    }



    async function generateIcon() {
        if (username) {

            const ctx = canvas.getContext('2d');
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Center coordinates
            const centerX = canvas.width / 2;
            const centerY = canvas.height / 2;

            // Radius of the outermost arc
            const outerRadius = 200;

            // Number of concentric arcs
            const numArcs = 18;

            // Draw concentric arcs
            function getFirstDigit(number) {
                // Convert the number to a string
                const numberStr = Math.abs(number).toString();

                // Get the first character (digit) of the string
                const firstDigit = numberStr.charAt(0);

                return parseInt(firstDigit, 10); // Convert the digit back to a number if needed
            }

            const hashedName = await sha256ToFloat(username);
            for (let i = 0; i < numArcs; i++) {

                // Make the angle and offset prime numbers so they don't have any chance of having same modulo
                const angle = Math.pow(-1, getFirstDigit(hashedName) % 2) * i * (hashedName % (353)) * (Math.PI / 180) ;
                const radius = i * outerRadius / numArcs; 
                const offset = Math.pow(-1, getFirstDigit(hashedName) % 2) * i * (hashedName % (359)) * (Math.PI / 180)
                
                ctx.beginPath();
                
                ctx.arc(centerX, centerY, radius, angle, angle + offset, false);
                
                // Choose a different color for each arc
                ctx.strokeStyle = `hsl(${hashedName % (25*i)}, 75%, 50%)`;
                ctx.lineWidth = 5;
                ctx.lineCap = 'round';
                ctx.stroke();
            }

        }
    }

    $: (username, generateIcon())

</script>


<div class="flex flex-col w-full h-screen pt-12 items-center space-y-2">

    <h1 class="text-xl font-bold">Identicon</h1>
    
    <canvas id="randomCurvesCanvas" width="400" height="400" bind:this={canvas} class="rounded-lg border-neutral-300 border-2"></canvas>


    <input class="rounded-md w-48 h-8 border-neutral-200 border-2 p-2" type="text" placeholder="Enter username" bind:value={username}>

    <div class="flex w-3/4 justify-start ">
        <div class="space-y-2">

            <div>

                <h2 class="text-lg font-bold">Desired Outcome</h2>
                <ul class="list-disc list-inside">   
                    <li><strong>Uniqueness: </strong>Each username should create a unique identicon</li>
                    <li><strong>Visually pleasing: </strong>Each identicon should be pleasing to look at and not strain the eyes</li>
                    <li><strong>Physical inspiration: </strong>The identicon should look semi-organic and similar to a fingerprint</li>
                </ul>
            </div>

            <div>

                <h2 class="text-lg font-bold">How properties are generated (total num of combinations: 114,054,300)</h2>
                <ul class="list-disc list-inside">   
                    <li><strong>Number of rings: </strong>18</li>
                    <li><strong>SHA256 hash to float: </strong>Pass each username through a SHA256 hash then convert that hash to a float</li>
                    <li><strong>Direction of spiral: </strong>Based upon the arity of the first digit of the hashed number</li>
                    <li><strong>Starting angle: </strong>Circle number (center is 0) multiplied by the modulo of the hash by 353. </li>
                    <li><strong>Ending angle offset: </strong>Circle number (center is 0) multiplied by the modulo of the hash by 359.</li>
                    <li><strong>Direction of offset: </strong>Based upon the arity of the first digit of the hashed number</li>
                    <li><strong>Circle color: </strong>HSL of hashed number modulo 25 times the circle number</li>
                    
                </ul>
            </div>
        </div>

    </div>

</div>
