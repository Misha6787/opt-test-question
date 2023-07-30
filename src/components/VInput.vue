<template>
	<input type="text" class="input" :placeholder="placeholder">
</template>

<script setup lang="ts">
	defineProps({
		placeholder: {
			type: String,
			required: false
		}
	});
</script>

<style lang="scss">
	.input {
		width: 100%;
		font-size: 16px;
		background-color: var(--white);
		border: 1px solid var(--border-color);
		border-radius: var(--border-radius-actions);
		padding: var(--base-scale) var(--normal-scale);
		&:focus {
			outline: none;
		}
	}
</style>
