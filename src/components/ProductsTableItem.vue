<template>
	<div v-if="!mobileTable" class="table-row">
		<div
			v-for="header in headers"
			:class="header.key === 'actions' ? 'action-column' : ''"
			class="table-column"
		>
			<div v-if="header.key === 'id'">
				<icon-burger class="handle"/>
				{{ item.id }}
			</div>

			<div v-else-if="header.key === 'actions'">
				<icon-vertical-dots class="action-icon" v-on:click="windowAction = !windowAction"/>
				<div class="action-window" v-if="windowAction">
					<div class="delete-item" v-on:click="$emit('deleteItem')">Удалить</div>
				</div>
			</div>

			<div v-else-if="header.key === 'unitName'">
				<v-input v-on:input="helpSearch" :id="'table-input-' + item.id"/>
			</div>

			<div v-else>
				<v-input />
			</div>
		</div>
	</div >

	<div class="table-wrapper-mobile" v-else>
		<label class="table-label">
			<p class="table-label__text">Действия</p>
			<icon-vertical-dots class="action-icon" v-on:click="windowAction = !windowAction"/>
			<div class="action-window" v-if="windowAction">
				<div class="delete-item" v-on:click="$emit('deleteItem')">Удалить</div>
			</div>
		</label>
		<label class="table-label">
			<p class="table-label__text">Наименование еденицы</p>
			<v-input v-on:input="helpSearch" :id="'table-input-' + item.id"/>
		</label>
		<label class="table-label">
			<p class="table-label__text">Цена</p>
			<v-input />
		</label>
		<label class="table-label">
			<p class="table-label__text">Кол-во</p>
			<v-input />
		</label>
		<label class="table-label">
			<p class="table-label__text">Название товара</p>
			<v-input />
		</label>
		<label class="table-label">
			<p class="table-label__text">Итого</p>
			<v-input />
		</label>

	</div>
</template>

<script setup lang="ts">
	import IconBurger from "@/components/icons/IconBurger.vue";
	import IconVerticalDots from "@/components/icons/IconVerticalDots.vue";
	import VInput from "@/components/VInput.vue";
	import {onMounted, Ref, ref} from "vue";

	const props = defineProps({
		item: {
			type: Object,
			required: true
		},
		headers: {
			type: Array,
			required: false
		},
		mobileTable: {
			type: Boolean,
			required: false
		}
	})

	defineEmits(['deleteItem']);

	const windowAction: Ref<boolean> = ref(false);
	const helpSearchItems = [
		'Мраморный щебень фр. 2-5 мм, 25кг',
		'Мраморный щебень фр. 2-5 мм, 25кг (белый)',
		'Мраморный щебень фр. 2-5 мм, 25кг (вайт)',
		'Мраморный щебень фр. 2-5 мм, 25кг, возврат',
		'Мраморный щебень фр. 2-5 мм, 1т',
	];
	let windowHelpSearch;

	const helpSearchActive: Ref = ref([]);

	const closeWindowsActions = (event) => {
		if (!event.target.closest('.action-icon')) {
			windowAction.value = false;
		}
		if (!event.target.closest('.window-help-search')) {
			windowHelpSearch.style.display = 'none';
		}
	}

	const helpSearch = (event) => {
		const target = event.target;
		const targetPosition = target.getBoundingClientRect();
		document.getElementById('window-help-search__input').value = 'table-input-' + props.item.id;

		const reg = new RegExp(target.value, 'g');

		helpSearchActive.value = [];
		windowHelpSearch.innerHTML = '';

		helpSearchItems.forEach(item => {
			if ((target.value.trim() !== '') && item.match(reg)) {
				windowHelpSearch.insertAdjacentHTML('afterbegin', `
					<div class="window-help-search__item" data-item="${item}">
						<b class="window-help-search__item--bolt">${item.match(reg)}</b>${item.replace(reg, '')}
					</div>
				`);
			}
		})

		windowHelpSearch.style.cssText = `
			display: block;
			width: ${targetPosition.width}px;
			top: ${targetPosition.top + 50}px;
			left: ${targetPosition.left}px;
		`;
	}

	const pasteHelpSearchText = (item) => {
		const idInput = document.getElementById('window-help-search__input').value;
		document.getElementById(idInput).value = item.target.dataset.item;
		windowHelpSearch.style.display = 'none';
	}

	document.addEventListener('click', closeWindowsActions)

	onMounted(() => {
		windowHelpSearch = document.querySelector('.window-help-search')
		windowHelpSearch.addEventListener('click', pasteHelpSearchText)
	})


</script>

<style lang="scss">
	.handle {
		cursor: grab;
	}

	.action-icon {
		width: 16px;
		height: 16px;
		cursor: pointer;
	}
	.action-column {
		position: relative;
	}
	.action-window {
		position: absolute;
		bottom: -20px;
		left: 20px;
		border-radius: var(--border-radius-actions);
		box-shadow: var(--box-shadow-window);
		padding: 7px 9px;
		z-index: 9;
		background-color: #ffffff;

		& .delete-item {
			width: 150px;
			color: #ae0a0a;
			cursor: pointer;
			transition: var(--transition);
			&:hover {
				color: #ff0000;
			}
		}
	}

	// mobile styles

	.table-wrapper-mobile {
		border-radius: var(--border-radius);
		border: 1px solid var(--border-color);
		background-color: var(--white);
		padding: var(--normal-scale);
		&:not(:last-child) {
			margin-bottom: var(--small-scale);
		}

		& .table-label {
			position: relative;
			display: block;
			&:not(:last-child) {
				margin-bottom: var(--normal-scale);
			}

			& .table-label__text {
				color: var(--black-soft);
				font-size: 10px;
				margin-bottom: var(--small-scale);
			}

			& .action-icon {
				height: 13px;
			}

			.action-window {
				bottom: -32px;
				left: 0;
			}
		}
	}

</style>
