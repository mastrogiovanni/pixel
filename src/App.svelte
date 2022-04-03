<script>
	import { onMount } from "svelte";

	let canvas;
	let text;
	let width = 200;
	let height = 200;

	let colors = {
		R: "#ff0000",
		G: "#00ff00",
		M: "#0000ff",
	};

	let colorKeys = Object.keys(colors);

	let pixel = [
		[0, 1, 2],
		[0, 0, 0],
		[1, 1, 2, 1, 1, 0],
		[2, 2, 1, 1, 0],
		[2, 1, 2, 1, 2],
	];

	function parse(text) {
		if (!text) {
			return;
		}
		let newPixel = [];
		const re = /([0-9]+)([a-zA-Z]+)/;
		let lines = text.split("\n");
		for (let line of lines) {
			let newPixelLine = []
			let comps = line.split(/\s+/);
			for (let comp of comps) {
				let parsed = re.exec(comp);
				if (parsed) {
					let color = parsed[2];
					if (!colors[color]) {
						colors[color] = 'green';
					}
					for (let i = 0; i < parsed[1]; i ++) {
						newPixelLine.push(color)
					}					
				}
			}
			newPixel.push(newPixelLine);
		}
		colors = colors;
		colorKeys = Object.keys(colors);
		pixel = newPixel;
		draw();
	}

	$: parse(text);

	function draw() {
		var ctx = canvas.getContext("2d");

		let rows = 10;
		let cols = 10;

		let dx = width / cols;
		let dy = height / rows;

		ctx.lineWidth = "0.2";
		ctx.fillStyle = "white";
		ctx.fillRect(0, 0, width, height);

		for (let i = 0; i <= rows; i++) {
			ctx.moveTo(i * dx, 0);
			ctx.lineTo(i * dx, height);
			ctx.stroke();
		}

		for (let i = 0; i <= cols; i++) {
			ctx.moveTo(0, i * dy);
			ctx.lineTo(width, i * dy);
			ctx.stroke();
		}

		ctx.moveTo(0, 0);
		for (let row in pixel) {
			let line = pixel[row];
			for (let col in line) {
				let color = line[col];
				if (colors[color]) {
					ctx.fillStyle = colors[color];
					ctx.moveTo(col * dx, row * dy);
					ctx.fillRect(col * dx, row * dy, dx, dy);
				}
			}
		}
	}

	onMount(() => {
		draw();
	});
</script>

<svelte:head>
	<script
		src="https://unpkg.com/tesseract.js@2.1.5/dist/tesseract.min.js"></script>
</svelte:head>

<div class="container">
	<h1>Matteo</h1>
	<div class="row">
		<div class="col">
			<textarea bind:value={text} />
			<br/>
			<ul>
				{#each colorKeys as colorKey}
				<li>
					<label for="exampleColorInput" class="form-label">{colorKey}</label>
					<input
						type="color"
						class="form-control form-control-color"
						id="exampleColorInput{colorKey}"
						bind:value={colors[colorKey]}
						title="Choose your color"
					/>
				</li>
				{/each}
			</ul>
		</div>
		<div class="col">
			<canvas bind:this={canvas} id="myCanvas" {width} {height} />
		</div>
	</div>
</div>
