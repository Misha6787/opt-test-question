<template>
	<section class="navigation-panel">
		<!-- ... -->
	</section>
</template>

<script setup>

</script>

<style lang="scss">
	.navigation-panel {
		position: absolute;
		width: var(--navigation-panel-widght);
		background-image: var(--image-navigation);
		height: 100%;
		top: 0;
	}
	@media screen and (max-width: 375px) {
		.navigation-panel {
			display: none;
		}
	}
</style>
