<template>
	<div class="table-contents">
		<div class="table-actions">
			<v-button class="btn__icon table-actions-btn" v-on:click="windowPreview = !windowPreview; windowFilter = false">
				<icon-gear />
			</v-button>
			<div class="table-actions-window">
				<div class="table-actions-window__preview"
				     v-show="windowPreview"
				     v-on:click="windowFilter = !windowFilter; windowPreview = !windowPreview;"

				>
					Отображение столбцов
					<icon-arrow-right class="table-actions-window__preview-icon"/>
				</div>
				<!-- /.table-actions-window__preview -->
				<div class="table-actions-window__filter" v-if="windowFilter" v-on:change="toggleFilter($event.target.value)">
					<div class="table-actions-window__title">
						<icon-arrow-right class="table-actions-window__title-icon"/>
						Отображение столбцов
					</div>
					<label class="table-actions-window__label">
						<input type="checkbox" value="0">
						Номер
					</label>
					<label class="table-actions-window__label">
						<input type="checkbox" value="1">
						Действия
					</label>
					<label class="table-actions-window__label">
						<input type="checkbox" value="2">
						Наименование еденицы
					</label>
					<label class="table-actions-window__label">
						<input type="checkbox" value="3">
						Цена
					</label>
					<label class="table-actions-window__label">
						<input type="checkbox" value="4">
						Кол-во
					</label>
					<label class="table-actions-window__label">
						<input type="checkbox" value="5">
						Название товара
					</label>
					<label class="table-actions-window__label">
						<input type="checkbox" value="6">
						Итого
					</label>

				</div>
				<!-- /.table-actions-window__filter -->
			</div>
			<!-- /.table-actions-window -->

		</div>
		<!-- /.page-content__table-actions -->
		<div class="table-wrapper">
			<table class="table"
		       v-if="!mobileTable"
		       v-on:mouseup="clearResizeData"
		       v-on:mousemove="resizeColumn($event)"
			>
				<thead>

					<draggable
						v-model="tableHeaders"
						tag="tr"
						:item-key="key => key"
						handle=".column-name"
					>
						<template #item="{ element: tableHeaders }">
							<th scope="col">
								<b class="column-name">{{ tableHeaders.name }}</b>
								<div class="line" v-on:mousedown="startResize($event)" />
							</th>
						</template>
					</draggable>

				</thead>
				<draggable
					v-model="tableList"
					item-key="id"
					v-on:end="$emit('updateList', tableList)"
					handle=".handle"
					tag="tbody"
				>
					<template #item="{element}">
						<products-table-item
							:item="element"
							:headers="tableHeaders"
							v-on:deleteItem="$emit('deleteItem', element.id)"
						/>
					</template>
				</draggable>
			</table>
			<!-- /.table -->

			<div v-else>
				<products-table-item
					v-for="element in tableList"
					item-key="element.id"
					:item="element"
					:mobileTable="mobileTable"
					v-on:deleteItem="$emit('deleteItem', element.id)"
				/>
			</div>
			<!-- /.mobile-items-wrapper -->

		</div>
		<!-- /.table-wrapper -->
		<products-table-calculation />
	</div>
	<!-- /.table-contents -->

    <window-help-search />
	<!-- /.window-help-search -->
</template>

<script setup lang="ts">
	import {onMounted, ref} from "vue";
	import draggable from "vuedraggable";

	import ProductsTableItem from "@/components/ProductsTableItem.vue";
	import IconGear from "@/components/icons/IconGear.vue";
	import ProductsTableCalculation from "@/components/ProductsTableCalculation.vue";
	import VButton from "@/components/VButton.vue";
	import IconArrowRight from "@/components/icons/IconArrowRight.vue";
	import WindowHelpSearch from "@/components/WindowHelpSearch.vue";

	const props = defineProps({
		list: {
			type: Array,
			required: true
		},
		headers: {
			type: Array,
			required: true
		}
	});

	defineEmits(['updateList', 'deleteItem']);

	const tableList = ref(props.list);
	const tableHeaders = ref(props.headers);
	const windowPreview = ref(false);
	const windowFilter = ref(false);
	const mobileTable = ref(false);

	let curCol: HTMLElement;
	let nxtCol: Element;
	let pageX: number;
	let nxtColWidth: number;
	let curColWidth: number;

	let table: HTMLElement;
	let tableWidth: number;

	const setHeightLines = ():void => {
		const tableHeight: number = document.querySelector('.table').offsetHeight - 2;
		const tableLines: NodeListOf<HTMLElement> = document.querySelectorAll('.table .line');
		tableLines.forEach(item => item.style.height = `${tableHeight}px`);
	}

	const startResize = (event: Event): void => {
		curCol = event.target.parentElement;
		nxtCol = curCol.nextElementSibling;
		pageX = event.pageX;

		curColWidth = curCol.offsetWidth;
		nxtColWidth = nxtCol ?? nxtCol.offsetWidth;

	}

	const resizeColumn = (event: Event): void => {
		if (curCol) {
			const diffX = event.pageX - pageX;

			const nxtColName = nxtCol.querySelector('.column-name');
			const curColName = curCol.querySelector('.column-name');

			// if (table.offsetWidth > tableWidth) {
			// 	curCol.style.width = curColWidth - 2 + 'px';
			// 	curColName.style.width = curColWidth - 2 + 'px';
			// 	return
			// }

			nxtCol.style.width = nxtCol ?? (nxtColWidth - (diffX))+'px';
			nxtColName.style.width = nxtCol ?? (nxtColWidth - (diffX))+'px';

			curCol.style.width = (curColWidth + diffX)+'px';
			curColName.style.width = (curColWidth + diffX)+'px';
		}
	}

	const clearResizeData = (): void => {
		curCol = undefined;
		nxtCol = undefined;
		pageX = undefined;
		nxtColWidth = undefined;
		curColWidth = undefined
	}

	const toggleFilter = (index): void => {
		const tableHeadColumns = table.querySelectorAll('thead th');
		const tableBodyItems = table.querySelectorAll('tbody td');
		const countColumns = tableHeadColumns.length;

		tableHeadColumns[index].style.display = tableHeadColumns[index].style.display === 'none' ? '' : 'none';

		for (let i = +index; i < tableBodyItems.length; i += countColumns) {
			tableBodyItems[i].style.display = tableHeadColumns[index].style.display;
		}

	}

	const closeWindowsFilters = (event): void => {
		if (!event.target.closest('.table-actions') ){
			windowPreview.value = false;
			windowFilter.value = false;
		}
	}

	const checkSizeWindow = (): void => {
		mobileTable.value = window.innerWidth <= 375
	};

	document.addEventListener('click', closeWindowsFilters)

	onMounted(() => {
		setHeightLines();
		table = document.querySelector('.table');
		tableWidth = table.offsetWidth;
		checkSizeWindow();
	});
</script>

<style lang="scss">
	.table-contents {
		border-radius: var(--border-radius);
		background-color: var(--white);
		box-shadow: var(--box-shadow);
		padding-top: 8px;
		padding-bottom: var(--xlarge-scale);

		& .table-actions {
			display: flex;
			position: relative;
			justify-content: end;

			& .table-actions-btn {
				padding: 0 var(--normal-scale);
				padding-bottom: 8px;
			}

			& .table-actions-window {
				position: absolute;
				display: flex;
				top: 20px;
				right: 15px;
				font-size: 14px;
			}

			& .table-actions-window__preview,
			& .table-actions-window__filter {
				position: relative;
				display: block;
				padding: 7px 10px;
				border-radius: var(--border-radius-actions);
				box-shadow: var(--box-shadow-window);
				background-color: #fff;
				z-index: 99;
			}

			& .table-actions-window__preview {
				height: fit-content;
				&:hover {
					cursor: pointer;
				}
			}

			& .table-actions-window__filter {
				display: flex;
				flex-direction: column;
				padding-right: 24px;
				margin-left: var(--large-scale);
			}

			& .table-actions-window__preview-icon {
				margin-left: var(--base-scale);

			}

			& .table-actions-window__title {
				font-weight: 600;
				margin-bottom: 14px;
			}

			& .table-actions-window__title-icon {
				transform: rotate(180deg);
				margin-right: var(--base-scale);
			}

			& .table-actions-window__label {
				&:not(:last-child) {
					margin-bottom: 14px;
				}
			}
		}

		& .table-wrapper {
			overflow-x: auto;
			padding-bottom: var(--large-scale);

			& .table {
				width: 100%;
				user-select: none;
				border-spacing: 0;

				& th {
					position: relative;
					border-top: 1px solid var(--border-color);
					border-bottom: 1px solid var(--border-color);
					text-align: start;
					&:not(:last-child) {
						border-right: 1px solid var(--border-color);
					}

					& .column-name {
						display: block;
						font-size: 16px;
						font-weight: 600;
						white-space: nowrap;
						overflow: hidden;
						padding: var(--normal-scale) var(--base-scale);
						margin-right: 0;
					}

					& .line {
						width: 2px;
						background-color: #bcbcbc;
						cursor: col-resize;
						position: absolute;
						top: 0;
						right: 0;
						opacity: 0;
						transition: var(--transition);
						padding: 3px;
						&:hover {
							opacity: 1 !important;
						}
					}
				}
				& tbody {
					& tr {
						position: relative;
						& td {
							padding: var(--small-scale) var(--normal-scale);

						}
					}
				}
			}
		}
	}

	.sortable-chosen {
		&:before {
			content: '';
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			border: dashed 2px #a6b7d4;
		}

		& .column-name {
			opacity: 0;
		}

		& td {
			opacity: 0;
		}
	}

	@media screen and (max-width: 375px) {
		.table-contents {
			background-color: unset;
			box-shadow: unset;
			padding-top: 0;
			padding-bottom: 0;
			& .table-actions {
				display: none;
			}
		}
	}
</style>
