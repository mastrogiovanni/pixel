<script>
	import { onMount } from "svelte";

	let canvas;
	let text;
	let width = 200;
	let height = 200;

    let posizione = 100;
    let altezza = 100;

    let distanza = 5;

    function draw() {
		var ctx = canvas.getContext("2d");

        ctx.fillStyle = 'cyan';
		ctx.fillRect(0, 0, 200, 200);

        ctx.fillStyle = 'red';
		ctx.fillRect(50, 50, 100, 100);

        ctx.fillStyle = 'green';
        ctx.beginPath();
		ctx.arc(posizione, altezza, 50, 1.5 * Math.PI, 2 * Math.PI);
        ctx.lineTo(posizione, altezza)
        ctx.fill();
    }

    onMount(() => {
        draw();
    });

    document.onkeypress = function (event) {
        let char = (typeof event !== 'undefined') ? event.keyCode : event.which
        if (char === 97) {
            // spostiamo a sinistra
            posizione = posizione - distanza
            if (posizione < -50) {
                posizione = 200
            }
            draw();
            return;
        }
        else if (char === 100) {
            // spostiamo a destra
            posizione = posizione + distanza
            if (posizione > 200) {
                posizione = -50
            }
            draw();
            return;
        }
        else if (char === 119) {
            // spostiamo sopra
            altezza = altezza - distanza
            if (altezza < 0) {
                altezza = 250
            }
            draw();
            return;
        }
        else if (char === 115) {
            // spostiamo sotto
            altezza = altezza + distanza
            if (altezza > 250) {
                altezza = 0
            }
            draw();
            return;
        }
        console.log(char);
    }

</script>

<canvas bind:this={canvas} id="myCanvas" {width} {height} />
