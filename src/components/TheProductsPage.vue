<template>
	<nav class="page-navigation">
		<ul class="page-navigation__list">
			<li class="page-navigation__item">
				<a href="#">Общее</a>
			</li>
			<li class="page-navigation__item page-navigation__item--active">
				<a href="#">Товар списания</a>
			</li>
			<li class="page-navigation__item">
				<a href="#">Товар списания</a>
			</li>
		</ul>
		<!-- /.page-navigation__list -->
	</nav>
	<!-- /.page-navigation -->

	<div class="page-actions">
		<v-button class="btn--primary" v-on:click="addItem"><icon-plus />Добавить строку</v-button>
	</div>
	<!-- /.page-actions -->

	<products-table
		:list="list"
		:headers="headers"
		v-on:updateList="updateList"
		v-on:deleteItem="deleteItem"
	/>
	<!-- /products-table -->
</template>

<script setup lang="ts">
	import IconPlus from "@/components/icons/IconPlus.vue";
	import VButton from "@/components/VButton.vue";
	import ProductsTable from "@/components/ProductsTable.vue";
	import {ref} from "vue";

	const headers = ref([
		{
			name: 'Номер',
			key: 'id',
		},
		{
			name: 'Действия',
			key: 'actions',
		},
		{
			name: 'Наименование еденицы',
			key: 'unitName',
		},
		{
			name: 'Цена',
			key: 'price',
		},
		{
			name: 'Кол-во',
			key: 'count',
		},
		{
			name: 'Название товара',
			key: 'name',
		},
		{
			name: 'Итого',
			key: 'total',
		}
	]);
	const list = ref([
		{
			id: 0,
			actions: '',
			unitName: '',
			price: '',
			count: '',
			name: '',
			total: ''
		},
		{
			id: 1,
			actions: '',
			unitName: '',
			price: '',
			count: '',
			name: '',
			total: ''
		},
		{
			id: 2,
			actions: '',
			unitName: '',
			price: '',
			count: '',
			name: '',
			total: ''
		},
		{
			id: 3,
			actions: '',
			unitName: '',
			price: '',
			count: '',
			name: '',
			total: ''
		}
	]);

	const setHeightLines = (): void => {
		const tableHeight: number = document.querySelector('.table').offsetHeight - 2;
		const tableLines: NodeListOf<HTMLElement> = document.querySelectorAll('.table .line');
		tableLines.forEach(item => item.style.height = `${tableHeight}px`);
	};

	const addItem = (): void => {
		list.value.push({id: list.value.length});
		setTimeout(() => setHeightLines());
	};

	const updateList = (newItems: any, newHeaders: any): void => {
		list.value = newItems;
		headers.value = newHeaders;
	};

	const deleteItem = (id: number): void => {
		list.value.forEach((item, index) => {
			if (item.id === id) {
				list.value.splice(index, 1);
			}
		});

		setTimeout(() => setHeightLines());
	};


</script>

<style lang="scss">
	.page-navigation {
		margin-bottom: var(--xlarge-scale);
		& .page-navigation__list {
			display: flex;
			list-style: none;
			padding: 0;
			& .page-navigation__item {
				& a {
					color: var(--light-navy);
					font-size: 16px;
					font-weight: 600;
					text-decoration: none;
					transition: var(--transition);
					&:hover {
						opacity: 0.7;
					}
				}
				&--active a{
					color: var(--black);
					pointer-events: none;
				}
				&:not(:last-child) {
					margin-right: var(--large-scale);
				}
			}
		}
	}

	.page-actions {
		margin-bottom: var(--xlarge-scale);
		border-radius: var(--border-radius);
		padding: var(--large-scale) var(--xlarge-scale);
		background-color: var(--white);
		box-shadow: var(--box-shadow);
		border: 1px solid var(--border-color);
	}
</style>
