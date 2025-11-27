<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<title>Mehmood Ahmad | Software Engineer Portfolio</title>
	<meta name="description" content="Portfolio of Mehmood Ahmad - Software Engineer specializing in AI, Computer Vision, VR, and Full-Stack Development" />
	<link rel="icon" href={favicon} />
</svelte:head>

{@render children()}
