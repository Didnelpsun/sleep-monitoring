<template>
	<view class="c">
		<view v-if="!open" class="c">
			<view class="img c" @click="open=!open">
				<image class="logo" src="/static/image/video.png"></image>
			</view>
			<view>
				<text class="title">{{title}}</text>
			</view>
		</view>
		<view v-if="open">
			<Camera></Camera>
		</view>
	</view>
</template>

<script>
	import { home } from '../../api/api.js'
	import Camera from '../../components/Camera/Camera.vue'
	export default {
		components:{
			Camera
		},
		data() {
			return {
				title: '点击图案开始录像',
				open: false
			}
		},
		created() {
			// console.log(home)
			uni.request({
				url:home,
				method: 'GET',
				success(res) {
					console.log(res)
				},
				fail(err) {
					console.log(err)
					console.log('获取数据失败')
				}
			})
		},
		methods: {
		}
	}
</script>

<style lang="scss" scoped>
	.c{
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.r{
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-around;
	}
	.img{
		margin: 100px 0 50px;
		height: 300rpx;
		width: 300rpx;
		border-radius: 50%;
		background-color: $uni-bg-color-hover;
	}
	.logo {
		height: 100rpx;
		width: 150rpx;
	}
	.title {
		font-size: $uni-font-size-lg;
		color: $uni-text-color-grey;
	}
</style>
