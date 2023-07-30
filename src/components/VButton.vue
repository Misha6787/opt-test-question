<template>
	<button class="btn">
		<slot />
	</button>
</template>

<script setup>

</script>

<style lang="scss">
	.btn {
		display: flex;
		align-items: center;
		font-size: 14px;
		border: none;
		border-radius: var(--border-radius-actions);
		background-color: var(--black-mute);
		padding: var(--base-scale) var(--normal-scale) var(--base-scale) var(--base-scale);
		cursor: pointer;
		transition: var(--transition);
		& svg {
			margin-right: 7px;
		}
		&:hover {
			opacity: 0.9;
		}
		&:active {
			opacity: 1;
			transform: scale(1.02);
		}
		&.btn__icon {
			padding: 0;
			background-color: unset;
			& svg {
				margin-right: 0;
			}
			&:hover {
				transform: scale(1.3);
			}
		}
		&--primary {
			background-color: var(--primary);
			color: var(--white);
		}
	}



</style>
