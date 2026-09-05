<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<title>Mehmood Ahmad — Software Engineer</title>
	<meta name="description" content="Mehmood Ahmad builds applied AI, computer vision, robotics, mobile products, and developer infrastructure." />
	<meta name="theme-color" content="#07111f" />
	<meta name="apple-mobile-web-app-capable" content="yes" />
	<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
	<meta property="og:title" content="Mehmood Ahmad — Software Engineer" />
	<meta property="og:description" content="Applied AI, real-time systems, mobile products, and developer infrastructure." />
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
