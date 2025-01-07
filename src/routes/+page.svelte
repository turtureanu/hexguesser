<script lang="ts">
	import Guesser from '../layout/Guesser.svelte';
	import Faq from '../layout/Faq.svelte';
</script>

<h1>Guess the <a href="https://developer.mozilla.org/en-US/docs/Web/CSS/hex-color">HEX</a>code!</h1>

<Guesser />
<Faq />

<style lang="scss">
	@use '../styles/variables.scss' as *;

	h1 {
		color: $dark;
		text-align: center;
		padding: 1em 0;
		font-weight: 900;
		font-size: 2.25rem;
	}
</style>
