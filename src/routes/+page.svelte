<script>
	import { filter } from '$lib';

	let windowHeight = undefined;
	let heightStyle = '100vh';

	let isDragging = false;
	let offsetX = 0;
	let offsetY = 0;
	let currentElement = null;

	// Array of background URLs:
	const imageURLs = [
		'https://i.imgur.com/DBiQQDc.jpeg',
		'https://i.imgur.com/230aEjp.jpeg',
		'https://i.imgur.com/NDQ5UkW.jpeg',
		'https://i.imgur.com/IZXdb9Z.jpeg',
		'https://i.imgur.com/0svz9Oa.jpeg',
		'https://i.imgur.com/UTeR0sR.jpeg'
	];

	// Reactive variable for the current background image:
	let currentBackground = '';

	// Function to switch to a random background image:
	function switchBackground() {
		const randomIndex = Math.floor(Math.random() * imageURLs.length);
		currentBackground = imageURLs[randomIndex];
	}

	// Call switchBackground on component mount:
	switchBackground();

	function onMouseDown(event) {
		isDragging = true;
		currentElement = event.currentTarget;
		offsetX = event.clientX - currentElement.getBoundingClientRect().left;
		offsetY = event.clientY - currentElement.getBoundingClientRect().top;
		document.addEventListener('mousemove', onMouseMove);
		document.addEventListener('mouseup', onMouseUp);
	}

	function onTouchStart(event) {
		isDragging = true;
		currentElement = event.currentTarget;
		const touch = event.touches[0];
		offsetX = touch.clientX - currentElement.getBoundingClientRect().left;
		offsetY = touch.clientY - currentElement.getBoundingClientRect().top;
		document.addEventListener('touchmove', onTouchMove);
		document.addEventListener('touchend', onTouchEnd);
	}

	function onMouseMove(event) {
		if (!isDragging) return;
		currentElement.style.left = `${event.clientX - offsetX}px`;
		currentElement.style.top = `${event.clientY - offsetY}px`;
	}

	function onTouchMove(event) {
		if (!isDragging) return;
		const touch = event.touches[0];
		currentElement.style.left = `${touch.clientX - offsetX}px`;
		currentElement.style.top = `${touch.clientY - offsetY}px`;
	}

	function onMouseUp() {
		isDragging = false;
		document.removeEventListener('mousemove', onMouseMove);
		document.removeEventListener('mouseup', onMouseUp);
	}

	function onTouchEnd() {
		isDragging = false;
		document.removeEventListener('touchmove', onTouchMove);
		document.removeEventListener('touchend', onTouchEnd);
	}

	// To disable scrolling:
	document.addEventListener(
		'touchmove',
		function (event) {
			event.preventDefault();
		},
		{ passive: false }
	);

	$: {
		heightStyle = `${windowHeight - 32}px`;
	}
</script>

<svelte:window bind:innerHeight={windowHeight} />

<main style="background-image: url({currentBackground}); height: {heightStyle}">
	<div class="overlay"></div>
	<h1>Liquid Glass</h1>
	<p>
		Quick and simple Liquid Glass material in CSS. The background distortion around the edges only
		works on Blink-based browsers, while the different blend modes only work on WebKit-based
		browsers. Everything else was recreated based on Apple’s Figma template. Switching between light
		and dark theme makes Liquid Glass behave differently. Frag the buttons below to see how it
		works.
	</p>
	<svg style="display: none" xmlns="http://www.w3.org/2000/svg">
		<filter height="100%" id="displacementDistortionFilter" width="100%" x="0px" y="0px">
			<feImage href={filter} result="colorMap" />
			<feDisplacementMap
				in="SourceGraphic"
				in2="colorMap"
				scale="25"
				xChannelSelector="R"
				yChannelSelector="G"
			/>
		</filter>
	</svg>
	<button
		class="liquid-glass liquid-glass-small"
		on:mousedown={onMouseDown}
		on:touchstart={onTouchStart}
	>
		<p>(Small) Distortion</p>
	</button>
	<button
		class="liquid-glass liquid-glass-medium"
		on:mousedown={onMouseDown}
		on:touchstart={onTouchStart}
	>
		<p>(Medium) Distortion</p>
	</button>
	<button
		class="liquid-glass liquid-glass-large"
		on:mousedown={onMouseDown}
		on:touchstart={onTouchStart}
	>
		<p>(Large) Distortion</p>
	</button>
</main>

<style>
	:root {
		color-scheme: light dark;
		/* Font families: */
		--sans-serif-font-family:
			ui-sans-serif, 'SF Pro', Roboto, 'Fira Sans', Oxygen, Ubuntu, 'Segoe UI', 'Helvetica Neue',
			'Noto Sans', Arial, sans-serif;
		--monospace-font-family:
			ui-monospace, 'SF Mono', 'Roboto Mono', 'Fira Mono', 'Oxygen Mono', 'Ubuntu Mono',
			'Cascadia Code', Menlo, 'Noto Sans Mono', Consolas, monospace;
		--symbol-font-family: 'Material Symbols Rounded';

		--overlay: rgb(255, 255, 255, 0.75);

		/* Liquid Glass: */
		--liquid-glass-outline: rgb(255, 255, 255, 0.5);
		--liquid-glass-primary-background-blend-mode: color-dodge;
		--liquid-glass-secondary-background-blend-mode: color-dodge;
		--liquid-glass-tertiary-background-blend-mode: plus-darker;

		--liquid-glass-small-background-blur-addend: 3px;
		--liquid-glass-medium-background-blur-addend: 10px;
		--liquid-glass-large-background-blur-addend: 12.5px;

		--liquid-glass-small-bg: rgb(247, 247, 247, 0.09);
		--liquid-glass-medium-bg: rgb(250, 250, 250, 0.07);
		--liquid-glass-large-bg: rgb(245, 245, 245, 0.1);
	}

	@media (prefers-color-scheme: dark) {
		:root {
			--overlay: rgb(0, 0, 0, 0.5);

			/* Liquid Glass: */
			--liquid-glass-outline: rgb(255, 255, 255, 0.25);
			--liquid-glass-primary-background-blend-mode: color-burn;
			--liquid-glass-secondary-background-blend-mode: unset;
			--liquid-glass-tertiary-background-blend-mode: plus-lighter;

			--liquid-glass-small-bg: rgb(0, 0, 0, 0.06);
			--liquid-glass-medium-bg: rgb(0, 0, 0, 0.05);
			--liquid-glass-large-bg: rgb(0, 0, 0, 0.05);
		}
	}

	/* Body styles: */

	* {
		-webkit-tap-highlight-color: transparent;
		font-family: var(--sans-serif-font-family);
		margin-block-end: 0px;
		margin-block-start: 0px;
		margin-inline-end: 0px;
		margin-inline-start: 0px;
		text-size-adjust: none;
	}

	*:focus {
		outline: unset;
	}

	main {
		background-position: center;
		background-size: cover;
		display: flex;
		flex-direction: column;
		gap: 16px;
		/* height: calc(100vh - 32px); */
		left: 0px;
		overflow: hidden;
		padding: 16px;
		position: absolute;
		top: 0px;
	}

	.overlay {
		background: linear-gradient(to bottom, var(--overlay), transparent);
		height: 50%;
		left: 0px;
		position: fixed;
		top: 0px;
		width: 100%;
		z-index: 0;
	}

	button {
		align-items: center;
		aspect-ratio: 1 / 1;
		border: unset;
		border-radius: 32px;
		display: flex;
		justify-content: center;
		max-width: 128px;
		padding: 32px;
	}

	button::before {
		border-radius: 32px;
	}

	button::after {
		border-radius: 32px;
	}

	/* Text styles: */

	h1,
	p {
		user-select: text;
		text-size-adjust: none;
		-webkit-user-select: text;
		z-index: 0;
	}

	h1 {
		/* font: 700 34px/41px; */
		font-size: 34px;
		font-weight: 700;
		line-height: 41px;
		text-size-adjust: none;
	}

	p {
		/* font: 400 17px/22px; */
		font-size: 17px;
		font-weight: 400;
		line-height: 22px;
		text-size-adjust: none;
	}

	/* Liquid Glass styles: */

	.liquid-glass {
		/* border: unset; */
		color: var(--labels-primary);
		cursor: grab;
		font-size: 17px;
		position: absolute;
		z-index: 1;
	}

	.liquid-glass::before {
		-webkit-backdrop-filter: unset;
		backdrop-filter: unset;
		content: '';
		height: 100%;
		left: 0px;
		mask-image: radial-gradient(black, transparent);
		mix-blend-mode: var(--liquid-glass-primary-background-blend-mode);
		position: absolute;
		top: 0px;
		width: 100%;
		z-index: 0;
	}

	.liquid-glass::after {
		/* mix-blend-mode: var(--liquid-glass-secondary-background-blend-mode); */
		/* opacity: 50%; */
		box-shadow: inset 1px 1px var(--liquid-glass-outline);
		content: '';
		height: 100%;
		left: 0px;
		position: absolute;
		top: 0px;
		width: 100%;
		z-index: 0;
	}

	.liquid-glass p {
		z-index: 1;
	}

	/* Liquid Glass size styles: */

	.liquid-glass-small {
		background: var(--liquid-glass-small-bg);
		left: calc(50% - 208px);
		top: calc(50% - 64px);
	}

	.liquid-glass-small::before {
		backdrop-filter: blur(calc(10px + var(--liquid-glass-small-background-blur-addend) / 2));
		-webkit-backdrop-filter: blur(
			calc(10px + var(--liquid-glass-small-background-blur-addend) / 2)
		);
	}

	.liquid-glass-small::after {
		backdrop-filter: blur(calc(5px + var(--liquid-glass-small-background-blur-addend) / 2));
		-webkit-backdrop-filter: blur(calc(5px + var(--liquid-glass-small-background-blur-addend) / 2));
	}

	.liquid-glass-medium {
		background: var(--liquid-glass-medium-bg);
		left: calc(50% - 64px);
		top: calc(50% - 64px);
	}

	.liquid-glass-medium::before {
		backdrop-filter: blur(calc(10px + var(--liquid-glass-medium-background-blur-addend) / 2));
		-webkit-backdrop-filter: blur(
			calc(10px + var(--liquid-glass-medium-background-blur-addend) / 2)
		);
	}

	.liquid-glass-medium::after {
		backdrop-filter: blur(calc(5px + var(--liquid-glass-medium-background-blur-addend) / 2));
		-webkit-backdrop-filter: blur(
			calc(5px + var(--liquid-glass-medium-background-blur-addend) / 2)
		);
	}

	.liquid-glass-large {
		background: var(--liquid-glass-large-bg);
		left: calc(50% + 80px);
		top: calc(50% - 64px);
	}

	.liquid-glass-large::before {
		backdrop-filter: blur(calc(10px + var(--liquid-glass-large-background-blur-addend) / 2));
		-webkit-backdrop-filter: blur(
			calc(10px + var(--liquid-glass-large-background-blur-addend) / 2)
		);
	}

	.liquid-glass-large::after {
		backdrop-filter: blur(calc(5px + var(--liquid-glass-large-background-blur-addend) / 2));
		-webkit-backdrop-filter: blur(calc(5px + var(--liquid-glass-large-background-blur-addend) / 2));
	}

	/* Only apply this to Blink instead of WebKit: */
	@supports (-webkit-app-region: inherit) {
		.liquid-glass-small {
			backdrop-filter: blur(calc(var(--liquid-glass-small-background-blur-addend) / 2))
				url(#displacementDistortionFilter);
		}

		.liquid-glass-medium {
			backdrop-filter: blur(calc(var(--liquid-glass-medium-background-blur-addend) / 2))
				url(#displacementDistortionFilter);
		}

		.liquid-glass-large {
			backdrop-filter: blur(calc(var(--liquid-glass-large-background-blur-addend) / 2))
				url(#displacementDistortionFilter);
		}
	}
</style>
