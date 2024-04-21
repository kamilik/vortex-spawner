<script>
	import Counter from './Counter.svelte';
	import welcome from '$lib/images/welcome.webp';
	import welcome_fallback from '$lib/images/welcome.gif';
	import { onMount } from 'svelte';
	


</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Vortex" />
</svelte:head>






<section>
	<h1>
		<span class="welcome">
			<picture>
				<source srcset={welcome} type="image/webp" />
				<img src={welcome_fallback} alt="Welcome" />
			</picture>
		</span>

		Welcome to Vortex<br/>
		account creator and manage
	
	</h1>
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		display: block;
		position: relative;
		width: 100%;
		height: 100%;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}

	.bad-1 {
		background-image: var(--background-image);
		background-size: var(--background-size);
		background-position: var(--background-position);
		width: var(--width);
		height: var(--height);
	}
</style>
