<script lang="ts">
	import { linear } from 'svelte/easing';
	import { slide } from 'svelte/transition';

	let isFAQToggled = $state(false);
	let isHowToggled = $state(false);
</script>

<div class="faq">
	<div class="faq-question">
		<button class="btn btn-expand" onclick={() => (isFAQToggled = !isFAQToggled)}
			>Why does this exist?</button
		>
		{#if isFAQToggled}
			<div transition:slide={{ duration: 100, axis: 'y', easing: linear }}>
				<p>
					A while back I came up with this project idea myself, but when I presented it to someone
					else, they said that it would be a nightmare and it wouldn't be fun at all. Fast-forward a
					few months and someone actually made it as part of <a
						href="https://high-seas.hackclub.com"
						target="_blank">High Seas</a
					>.
					<br />
					<br />
					This project is my attempt at making a better implementation and proving that the idea is not
					as bad as it might seem on the surface.
				</p>
			</div>
		{/if}
	</div>

	<div class="faq-question">
		<button class="btn btn-expand" onclick={() => (isHowToggled = !isHowToggled)}
			>How does it work?</button
		>
		{#if isHowToggled}
			<div transition:slide={{ duration: 100, axis: 'y', easing: linear }}>
				<p>
					A random color is presented on the screen, your goal is to guess the <a
						href="https://developer.mozilla.org/en-US/docs/Web/CSS/hex-color"
						target="_blank">HEX color code</a
					>. After entering your guess you can click on "Show answer" to see how close you actually
					were.
				</p>
			</div>
		{/if}
	</div>
</div>

<style lang="scss">
	@use '../styles/variables.scss' as *;
	@use '../styles/button.scss' as *;

	.btn-expand {
		width: 100%;
	}

	.faq {
		display: flex;
		margin: 0 auto;
		width: 80%;
		max-width: 600px;
		flex-direction: column;
		gap: 0.5rem 0;
	}
</style>
