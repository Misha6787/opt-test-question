<template>
	<the-navigation-panel />
	<!-- /.navigation-panel -->

	<section class="page-content">
		<h1 class="page-title">Проведение ТО и мелкий ремонт</h1>
		<!-- /.page-title -->

		<the-products-page />
	</section>
	<!-- /.main-content -->

</template>

<script setup lang="ts">
	import TheProductsPage from "@/components/TheProductsPage.vue";
	import TheNavigationPanel from "@/components/TheNavigationPanel.vue";

</script>

<style lang="scss">
	.page-content {
		flex: 1;
		margin-left: var(--navigation-panel-widght);
		padding: var(--xlarge-scale);
	}

	.page-title {
		font-size: 30px;
		margin-bottom: var(--xlarge-scale);
	}

	@media screen and (max-width: 375px) {
		.page-content {
			margin-left: 0;
			padding: var(--normal-scale) var(--base-scale);
		}

		.page-title {
			width: 295px;
		}

	}

</style>
