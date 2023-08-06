<template>
	<div class="page-content__table-calculation">
		<div class="page-content__table-calculation-content">
			<div class="page-content__table-calculation__item">
				<div class="page-content__table-calculation__item-name">Сумма:</div>
				<div class="page-content__table-calculation__item-value">152 212 руб</div>
			</div>
			<!-- /.page-content__table-calculation__item -->
			<div class="page-content__table-calculation__item">
				<div class="page-content__table-calculation__item-name">Кол-во:</div>
				<div class="page-content__table-calculation__item-value">24 шт</div>
			</div>
			<!-- /.page-content__table-calculation__item -->
			<div class="page-content__table-calculation__item">
				<div class="page-content__table-calculation__item-name">Общий вес:</div>
				<div class="page-content__table-calculation__item-value">2 322 кг</div>
			</div>
			<!-- /.page-content__table-calculation__item -->
		</div>
		<!-- /.page-content__table-calculation-content -->
		<div class="page-content__table-calculation-total">
			<div class="page-content__table-calculation__item-name">Общая сумма:</div>
			<div class="page-content__table-calculation__item-value">152 212 руб</div>
		</div>
		<!-- /.page-content__table-calculation-total -->
	</div>
</template>

<script setup>

</script>

<style lang="scss">
	.page-content__table-calculation-content,
	.page-content__table-calculation-total {
		padding: var(--normal-scale);
		border: 1px solid var(--border-color);
		border-radius: var(--border-radius-actions);
		background-color: var(--white-soft);
	}

	.page-content__table-calculation__item,
	.page-content__table-calculation-total {
		display: flex;
		justify-content: space-between;
	}

	.page-content__table-calculation__item-name,
	.page-content__table-calculation__item-value {
		font-size: 14px;
	}

	.page-content__table-calculation {
		width: 304px;
		margin-left: auto;
		margin-right: var(--normal-scale);

		& .page-content__table-calculation-content {
			margin-bottom: var(--small-scale);

			& .page-content__table-calculation__item {
				&:not(:last-child) {
					margin-bottom: var(--normal-scale);
				}

				& .page-content__table-calculation__item-name {
					color: var(--black-soft);
				}
			}
		}

	}

	.page-content__table-calculation-total {
		& .page-content__table-calculation__item-value {
			font-weight: 600;
		}
	}

	@media (max-width: 768px) {
		.page-content__table-calculation {
			width: 100%;
		}
	}

</style>
