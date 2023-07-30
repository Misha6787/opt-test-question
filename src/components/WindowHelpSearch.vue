<template>
	<div class="window-help-search">
		<div class="window-help-search__item">

		</div>
	</div>
	<input type="hidden" id="window-help-search__input">
</template>

<script setup lang="ts">

</script>


<style lang="scss">
	.window-help-search {
		position: fixed;
		display: none;
		border-radius: var(--border-radius-actions);
		box-shadow: var(--box-shadow-window);
		cursor: pointer;
		background-color: var(--white);
		& .window-help-search__item {
			padding: 5px 9px 5px 9px;
			&:first-child {
				padding-top: 7px;
				&:hover {
					border-top-left-radius: var(--border-radius-actions);
					border-top-right-radius: var(--border-radius-actions);
				}
			}
			&:last-child {
				padding-bottom: 7px;
				&:hover {
					border-bottom-left-radius: var(--border-radius-actions);
					border-bottom-right-radius: var(--border-radius-actions);
				}
			}
			&:hover {
				background-color: var(--white-mute);
			}

			& .window-help-search__item--bolt {
				font-weight: 600;
			}
		}
	}
</style>
