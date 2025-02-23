<script lang="ts">
	import { onMount } from 'svelte';
	import { fade, fly } from 'svelte/transition';

	let colors: string[] = $state([]);
	let generatedColor: string = $state('');
	let isAssistToggled: boolean = $state(false);
	let isAnswerShown = $state(false);

	const getUserHex = () => {
		return colors.length === 6 ? '#' + colors.join('') : '';
	};

	const generateRandomHex = () => {
		const r = Math.floor(Math.random() * 256);
		const g = Math.floor(Math.random() * 256);
		const b = Math.floor(Math.random() * 256);
		// snippet from https://stackoverflow.com/a/5624139
		generatedColor = '#' + ((1 << 24) | (r << 16) | (g << 8) | b).toString(16).slice(1);
		colors = [];
		// also hide the answer for the next color
		isAnswerShown = false;
	};

	generateRandomHex();

	// https://learnersbucket.com/examples/interview/convert-hex-color-to-rgb-in-javascript/
	const hexToRGB = (hex: string): number[] => {
		return [
			parseInt(hex.slice(1, 3), 16),
			parseInt(hex.slice(3, 5), 16),
			parseInt(hex.slice(5, 7), 16)
		];
	};

	const getScore = $state(() => {
		const [redUser, greenUser, blueUser] = hexToRGB('#' + colors.join(''));
		const [redGenerated, greenGenerated, blueGenerated] = hexToRGB(generatedColor);

		let diff = 0;

		diff += 256 - Math.abs(redGenerated - redUser);
		diff += 256 - Math.abs(greenGenerated - greenUser);
		diff += 256 - Math.abs(blueGenerated - blueUser);

		diff = diff / 3 / (256 / 100);

		return diff ? diff : 0;
	});

	// addEventListeners should start after mount
	onMount(() => {
		const inputs = document.getElementById('inputs');
		inputs!.addEventListener('keyup', function (e) {
			const target = e.target as HTMLInputElement;
			const key = e.key;

			const prev = target.previousElementSibling as HTMLInputElement | null;
			const next = target.nextElementSibling as HTMLInputElement | null;

			switch (key) {
				case 'ArrowRight':
					if (next) next.focus();
					break;
				case 'ArrowLeft':
					if (prev) prev.focus();
					break;
				default:
					// get the index of the input
					const index = Array.from(inputs!.children).indexOf(target);
					if (key.length === 1 && /[a-fA-F0-9]/.test(key) !== false) {
						const isNext = target.value === '' ? false : true;

						// use the index to set the index in the colors[]
						colors[index] = key;

						// if the current input is not empty and the next value is empty
						// then focus onto the next input
						isNext && next && next.value.length !== 1 && next.focus();
					} else {
						target.value = '';
						colors[index] = '';
					}
			}
		});
	});
</script>

<div class="square" style={`background-color: ${generatedColor}`}></div>

<div class="how-far-container">
	<div
		class="how-far"
		style={`width: ${getScore()}%; background-color: ${getScore() < 33 ? 'red' : getScore() >= 33 && getScore() <= 66 ? 'orange' : 'lightgreen'}`}
	></div>
</div>

{#if isAnswerShown}
	<p class="answer" transition:fade={{ duration: 300 }} style={`color:${generatedColor}`}>
		{generatedColor}
	</p>
{/if}

<div class="inputs" id="inputs">
	<input bind:value={colors[0]} class="input-red" type="text" maxlength="1" />
	<input bind:value={colors[1]} class="input-red" type="text" maxlength="1" />

	<input bind:value={colors[2]} class="input-green" type="text" maxlength="1" />
	<input bind:value={colors[3]} class="input-green" type="text" maxlength="1" />

	<input bind:value={colors[4]} class="input-blue" type="text" maxlength="1" />
	<input bind:value={colors[5]} class="input-blue" type="text" maxlength="1" />
</div>

{#if isAssistToggled}
	{#key colors}
		<div class="square square-preview" style={`background-color: ${getUserHex()}`}></div>
	{/key}
{/if}

{#if getUserHex() == generatedColor}
	<p class="win" transition:fly style={`color: ${generatedColor}`}>You actually got it!</p>
{/if}

<div class="btn-container">
	<button class="btn" onclick={generateRandomHex}>New color</button>
	<button class="btn" onclick={() => (isAssistToggled = !isAssistToggled)}>Toggle assist</button>
	<button class="btn" onclick={() => (isAnswerShown = !isAnswerShown)}>Show answer</button>
	<button class="btn" onclick={() => (colors = [])}>Clear input</button>
</div>

<style lang="scss">
	@use '../styles/button.scss' as *;
	@use 'sass:math';
	@use '../styles/variables' as *;

	$input-size: 2.625rem;

	.btn-container {
		display: flex;
		flex-wrap: wrap;
		gap: 1rem;
		align-items: center;
		justify-content: center;
		padding: 0.5em;
		margin: 2em auto;
		width: fit-content;
	}

	.inputs {
		width: fit-content;
		margin: 2rem auto;
	}

	.square {
		width: 80%;
		max-width: 350px;
		aspect-ratio: 1 / 1; // width and height are both the same
		margin: 2rem auto;
	}

	.square-preview {
		width: 50% !important;
		background: $dark;
		max-width: 175px;
	}

	input {
		text-align: center;
		width: $input-size;
		font-size: math.div($input-size, 1.25);
		aspect-ratio: 1 / 1;
		font-family: monospace;
		margin: 0;
		caret-color: transparent;

		&:focus,
		&:focus-visible {
			text-decoration: underline;
			border: 2px solid $dark;
			outline: none;
		}
	}

	.how-far-container {
		width: 80%;
		max-width: 346px;
		margin: 0 auto;
		outline: solid black 2px;
	}

	.how-far {
		max-width: 346px;
		height: 1.5rem;
	}

	.input-red {
		border: 2px dashed hsl(0, 50%, 50%);
	}

	.input-green {
		border: 2px dashed hsl(120, 50%, 50%);
	}

	.input-blue {
		border: 2px dashed hsl(240, 50%, 50%);
	}

	.answer {
		font-size: 1.5rem;
		text-align: center;
		text-shadow: 0.1ch 0.1ch 0 $dark;
	}

	.win {
		font-size: 2rem;
		text-align: center;
		font-weight: 900;
	}
</style>
