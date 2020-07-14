<template>
	<view class="card">
		<slot name="title"></slot>
		<slot name="value"></slot>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="scss" scoped>
.card{
	font-size: $uni-font-size-base;
	background-color: $uni-bg-color;
	border-color: $uni-bg-color-hover;
	// border-color: red;
	border-width: 1px;
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	align-items: center;
	color: $uni-text-color;
	margin: 20px 20px 20px;
}
</style>
