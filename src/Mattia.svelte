<script>
    import { onMount } from "svelte";


    // Programma
    let canvas;
    let H = 500; // Altezza sfondo
    let W = 500; // Larghezza quadro
    let rotazione = 0; // Rotazione della bandiera
    let direzione = 1;

    let italiaX = 900
    let italiaY = 1033
    let italia;

    let posX = 0;
    let posY = 0;

    function clear(ctx) {
        ctx.fillStyle = "white";
        ctx.fillRect(0, 0, W, H);

        // ctx.globalAlpha = 0.5;
        if (italia) {
            ctx.drawImage(italia, posX, posY, italiaX * 5, italiaY * 5);
        }
        // ctx.globalAlpha = 1;

    }

    function draw(ctx) {
        // Cosa fare all'inizio

        ctx.fillStyle = "cyan";
        ctx.fillRect(0, 0, W / 3, H / 2);

        ctx.fillStyle = "blue";
        ctx.fillRect(W / 3, 0, W / 3, H / 2);

        ctx.fillStyle = "white";
        ctx.fillRect(2 * W / 3, 0, W / 3, H / 2);

        ctx.strokeStyle = "black";
        ctx.beginPath();
        ctx.rect(0, 0, W, H / 2);
        ctx.stroke();

        ctx.fillStyle = "white"
        ctx.font = '128px serif';
        ctx.fillText('N', W / 2 - 40, H / 4 + 40);
    }

    function ruotaEDisegna() {
        var ctx = canvas.getContext("2d");

        rotazione = rotazione + 3 * direzione;

        clear(ctx);

        ctx.save();
        ctx.translate(500, 400);
        ctx.rotate(2 * Math.PI / 360 * rotazione);
        ctx.translate(-W / 2, -H / 4);
        
        draw(ctx)
        
        ctx.restore();        
    }

    onMount(() => {
        setInterval(ruotaEDisegna, 40)
    })

    document.onkeyup = function (event) {
        console.log("up")
    }

    document.onkeydown = function (event) {
        console.log(event);
        let char = typeof event !== "undefined" ? event.keyCode : event.which;
        if (char === 97) {
            posX = posX + 20;
            return;
        } else if (char === 100) {
            posX = posX - 20;
            return;
        } else if (char === 119) {
            posY = posY + 20;
            return;
        } else if (char === 115) {
            posY = posY - 20;
            return;
        }
        console.log(char);
    };

</script>

<canvas bind:this={canvas} id="myCanvas" width="1000" height="1000" />

<div style="display:none;">
    <img
        bind:this={italia}
        id="italia"
        src="/imgs/italia.jpeg"
        width={italiaX}
        height={italiaY}
        alt="italia"
    />
</div>