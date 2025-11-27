<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<title>Mehmood Ahmad | Software Engineer Portfolio</title>
	<meta name="description" content="Portfolio of Mehmood Ahmad - Software Engineer specializing in AI, Computer Vision, VR, and Full-Stack Development" />
	<meta name="theme-color" content="#000000" />
	<meta name="apple-mobile-web-app-capable" content="yes" />
	<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
