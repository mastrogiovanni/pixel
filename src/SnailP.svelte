<script>
    import { onMount } from "svelte";
    import { swipe } from "svelte-gestures";

    let canvas;

    let punti = 0;

    let maxW;
    let maxH;

    $: {
        W = maxW - 25;
        H = maxH - 110;
    }

    let H = 500; // Altezz sfondo
    let W = 500; // Larghezza quadro

    let quadratoX = 25;
    let quadratoY = 25;

    let vx = 0;
    let vy = 5;

    let velocita = 5;

    let play = true;

    const raggio = 10;

    let direction;
    function handler(event) {
        if (!play) {
            return;
        }
        direction = event.detail.direction;
        if (direction === "top") {
            moveTop();
        }
        else if (direction === "bottom") {
            moveBottom();
        }
        else if (direction === "left") {
            moveLeft();
        }
        else if (direction === "right") {
            moveRight();
        }
    }

    function touchStart() {
        if (!play) {
            restart();
        }
    }

    function draw() {
        var ctx = canvas.getContext("2d");

        ctx.fillStyle = "white";
        ctx.fillRect(0, 0, W, H);
        if (play) {
            // Erba
            ctx.globalAlpha = 0.5;
            ctx.drawImage(erba, 0, 0, W, H);
            ctx.globalAlpha = 1;
        } else {
            ctx.fillStyle = "red";
            ctx.fillRect(0, 0, W, H);
        }

        ctx.strokeStyle = "black";
        ctx.beginPath();
        ctx.rect(0, 0, W, H);
        ctx.stroke();

        // Cerchio
        // ctx.fillStyle = 'orange';
        // ctx.beginPath();
        // ctx.arc(quadratoX, quadratoY, raggio, 0, 2 * Math.PI);
        // ctx.fill();

        // Serpente
        ctx.save();
        ctx.translate(quadratoX, quadratoY);
        if (vx < 0) {
        } else if (vx > 0) {
            // Destra
            ctx.scale(-1, 1);
        } else if (vy < 0) {
            // Alto
            ctx.rotate(0.5 * Math.PI);
        } else if (vy > 0) {
            // Basso
            ctx.rotate(1.5 * Math.PI);
        }

        ctx.translate(-quadratoX, -quadratoY);
        ctx.drawImage(
            snake,
            quadratoX - raggio,
            quadratoY - raggio,
            2 * raggio,
            2 * raggio
        );
        ctx.restore();
    }

    function update() {
        quadratoX = quadratoX + vx;
        quadratoY = quadratoY + vy;
        check();
        draw();
    }

    function restart() {
        velocita = 2; // Velocita iniziale
        punti = 0; // Punti a zero
        quadratoX = 25; // Posizione iniziale del verme
        quadratoY = 25;
        vx = 0; // Velocità iniziale del verme
        vy = velocita;
        timerVelocita = setInterval(accelerazione, 5000); // Timer di aggiornamento velocita
        play = true; // Sei in gioco!
    }

    function lost() {
        vx = 0;
        vy = 0;
        velocita = 0;
        play = false;
        clearInterval(timerVelocita);
    }

    // Quando superi i bordi => hai perso
    function check() {
        if (quadratoX + raggio > W) {
            lost();
            return;
        }
        if (quadratoX - raggio < 0) {
            lost();
            return;
        }
        if (quadratoY + raggio > H) {
            lost();
            return;
        }
        if (quadratoY - raggio < 0) {
            lost();
            return;
        }
    }

    function accelerazione() {
        velocita = velocita + 1;
        if (vx > 0) {
            vx = velocita;
        }
        if (vx < 0) {
            vx = -velocita;
        }
        if (vy > 0) {
            vy = velocita;
        }
        if (vy < 0) {
            vy = -velocita;
        }
    }

    function score() {
        punti = punti + velocita;
    }

    let timerVelocita;

    onMount(() => {
        // draw();
        setInterval(update, 16);
        timerVelocita = setInterval(accelerazione, 7000);
        setInterval(score, 1000);
        restart();
    });

    function moveLeft() {
        // spostiamo a sinistra
        vx = -velocita;
        vy = 0;
    }

    function moveRight() {
        // spostiamo a destra
        vx = velocita;
        vy = 0;
    }

    function moveTop() {
        // spostiamo sopra
        vx = 0;
        vy = -velocita;
    }

    function moveBottom() {
        // spostiamo sotto
        vx = 0;
        vy = velocita;
    }

    document.onkeypress = function (event) {
        if (!play) {
            restart();
            return;
        }

        let char = typeof event !== "undefined" ? event.keyCode : event.which;
        if (char === 97) {
            moveLeft();
            return;
        } else if (char === 100) {
            moveRight()
            return;
        } else if (char === 119) {
            moveTop();
            return;
        } else if (char === 115) {
            moveBottom();
            return;
        }
        console.log(char);
    };

    let snake;
    let erba;
</script>

<svelte:window bind:innerWidth={maxW} bind:innerHeight={maxH} />

<div
    on:touchstart={touchStart}
    use:swipe={{
        timeframe: 300,
        minSwipeDistance: 100,
    }}
    on:swipe={handler}
    style="width: 100%; height: 100%"
>
    <h1>Punti {punti}</h1>

    Posizione: ({quadratoX}, {quadratoY})<br />
    Velocità: ({velocita})<br />

    <canvas bind:this={canvas} id="myCanvas" width={W} height={H} />

    <div style="display:none;">
        <img
            bind:this={snake}
            id="source"
            src="/imgs/snake2.png"
            width="640"
            height="640"
            alt="snake"
        />
        <img
            bind:this={erba}
            id="erba"
            src="/imgs/erba.jpg"
            width="970"
            height="545"
            alt="erba"
        />
    </div>
</div>
