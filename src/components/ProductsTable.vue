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

		<div class="table-wrapper" v-if="!mobileTable">
			<draggable
				v-model="tableHeaders"
				tag="div"
				:item-key="key => key"
				v-on:end="correctHeadCells"
				handle=".table-column-name"
				class="table-row table-head"
			>
				<template #item="{ element: tableHeaders }">
					<div class="table-column">
						<b class="table-column-name">{{ tableHeaders.name }}</b>
						<span class="resize-handle" />
					</div>
				</template>
			</draggable>

			<draggable
				v-model="tableList"
				item-key="id"
				tag="div"
				class="table-body"
				v-on:end="$emit('updateList', tableList)"
				handle=".handle"
			>
				<template #item="{element}">
					<products-table-item
						:item="element"
						:headers="tableHeaders"
						v-on:deleteItem="$emit('deleteItem', element.id)"
					/>
				</template>
			</draggable>

		</div>
		<!-- /.table-wrapper -->

		<div v-else>
			<products-table-item
				v-for="element in tableList"
				item-key="element.id"
				:item="element"
				:mobileTable="mobileTable"
				v-on:deleteItem="$emit('deleteItem', element.id)"
			/>
		</div>

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

	const min: number = 30;
	let headerBeingResized: HTMLElement | null;

	let tableWrapper: HTMLElement;
	let table: HTMLElement;

	const setHeightLines = (): void => {
		const tableHeight: number = tableWrapper.offsetHeight - 20;
		const tableLines: NodeListOf<HTMLElement> = tableWrapper.querySelectorAll('.resize-handle');
		tableLines.forEach(item => item.style.height = `${tableHeight}px`);
	}

	// TODO Нужно переписать
	const toggleFilter = (index): void => {
		const tableHeadColumns: NodeListOf<HTMLElement> = table.querySelectorAll('thead th');
		const tableBodyItems: NodeListOf<HTMLElement> = table.querySelectorAll('tbody td');
		const countColumns = tableHeadColumns.length;

		tableHeadColumns[index].style.display = tableHeadColumns[index].style.display === 'none' ? '' : 'none';

		for (let i = +index; i < tableBodyItems.length; i += countColumns) {
			tableBodyItems[i].style.display = tableHeadColumns[index].style.display;
		}
	}

	const closeWindowFilters = (event: Event): void => {
		if (!event.target.closest('.table-actions') ){
			windowPreview.value = false;
			windowFilter.value = false;
		}
	}

	const hideLastLine = (): void => {
		const lines: NodeListOf<HTMLElement> = document.querySelectorAll('.resize-handle');
		lines[lines.length - 1].style.display = 'none';
	}

	// Обновление размеров столбцов и синхронизация столбцов на странице с обьектом внутри
	const correctHeadCells = () => {
		const tableRow: NodeListOf<HTMLElement> = document.querySelectorAll('.table-row');
		tableRow.forEach((item: HTMLElement): void => {
			item.style.gridTemplateColumns = tableHeaders.value
				.map(({ header, size }) => size)
				.join(' ');
		})
	}

	const onMouseMove = (event: Event) => requestAnimationFrame(() => {
		const horizontalScrollOffset: number = document.documentElement.scrollLeft;
		const width: number = (horizontalScrollOffset + event.clientX) - headerBeingResized.getBoundingClientRect().left;

		let indexCurrentHead: number;

		// Обновление объекта столбца с новым значением размера и присваивание индекса текущего столбца
		const column = tableHeaders.value.find(({ header }, index) => {
			if (header === headerBeingResized) {
				indexCurrentHead = index
				return true;
			}
		});

		let tableWidth: number = 0;

		const tableColumns: NodeListOf<HTMLElement> = document.querySelectorAll('.table-head .table-column');

		tableColumns.forEach(item => {
			tableWidth += +item.offsetWidth;
		})

		const additionalWidth: number = Math.floor(((tableWrapper.offsetWidth - tableWidth) > 0 ? (tableWrapper.offsetWidth - tableWidth) : 0) / ((tableHeaders.value.length - 1) - indexCurrentHead));

		column.size = Math.max(min, width) + 'px'; // Enforce our minimum

		// Для других заголовков, которые не имеют установленной ширины, зафиксируйте ее на вычисленной ширине.
		tableHeaders.value.forEach((column) => {
			if(column.size.startsWith('minmax')) {
				column.size = parseInt(column.header.clientWidth, 10) + 'px';
			}
		});

		for (let i = indexCurrentHead+1; i < tableHeaders.value.length; i++) {
			tableHeaders.value[i].size = +tableHeaders.value[i].size.replace(/[^0-9]/g,"") + additionalWidth + 'px';
		}

		correctHeadCells();
	});

	const onMouseUp = () => {
		console.log('onMouseUp');

		window.removeEventListener('mousemove', onMouseMove);
		window.removeEventListener('mouseup', onMouseUp);
		headerBeingResized.classList.remove('header__being-resized');
		headerBeingResized = null;
	};

	const initResize = ({ target }) => {
		console.log('initResize');

		headerBeingResized = target.parentNode;
		window.addEventListener('mousemove', onMouseMove);
		window.addEventListener('mouseup', onMouseUp);
		headerBeingResized.classList.add('header__being-resized');
	};

	const checkSizeWindow = (): void => {
		mobileTable.value = window.innerWidth <= 375
	};

	document.addEventListener('click', closeWindowFilters)

	onMounted(() => {
		tableWrapper = document.querySelector('.table-wrapper');
		setHeightLines();
		hideLastLine();
		checkSizeWindow();
		document.querySelectorAll('.table-head .table-column').forEach((header: HTMLElement, index: number) => {
			const max = 1 + 'fr';
			tableHeaders.value[index].header = header;
			tableHeaders.value[index].size = `minmax(${min}px, ${max})`;

			header.querySelector('.resize-handle').addEventListener('mousedown', initResize);
		});
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
			margin-bottom: var(--large-scale);

			& .table-row {
				position: relative;
				min-width: 100%;
				width: auto;
				flex: 1;
				display: grid;
				border-collapse: collapse;
				user-select: none;
				grid-template-columns:
				    minmax(150px, 1fr)
				    minmax(150px, 1fr)
				    minmax(150px, 1fr)
				    minmax(150px, 1fr)
				    minmax(150px, 1fr)
				    minmax(150px, 1fr)
				    minmax(150px, 1fr);

				& .table-column,
				& .table-column-name {
					position: relative;
					overflow: hidden;
					white-space: nowrap;
				}
			}

			& .table-head {
				& .table-column {
					border-top: 1px solid var(--border-color);
					border-bottom: 1px solid var(--border-color);
					text-align: start;
					overflow: unset;
					&:not(:last-child) {
						border-right: 1px solid var(--border-color);
					}

					& .table-column-name {
						display: block;
						text-align: left;
						font-size: 16px;
						font-weight: 600;
						padding: var(--normal-scale) var(--base-scale);
					}

					& .resize-handle {
						width: 6px;
						background-color: #bcbcbc;
						cursor: col-resize;
						height: 100%;
						position: absolute;
						top: 0;
						right: -4px;
						opacity: 0;
						transition: var(--transition);
						z-index: 9;
						&:hover {
							opacity: 1 !important;
						}
					}
				}
			}

			& .table-body {
				& .table-column {
					display: flex;
					align-items: center;
					padding: var(--small-scale) var(--normal-scale);

					& div {
						width: 100%;
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

		& .table-column,
		& .table-column-name {
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
