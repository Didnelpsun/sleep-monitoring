<template>
	<view class="c">
		<view v-if="!open" class="c">
			<button class="img c" @click="openCamera" open-type="getUserInfo">
				<image class="logo" src="/static/image/video.png"></image>
			</button>
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
			openCamera(){
				let that = this
				let check = (res, url)=>{
					if(!res){
						console.log(res)
						wx.authorize({
							scope:url,
							success(){
								return true
							},
							fail(){
								console.log(res)
								return false
							}
						})
					}
					else{
						return true
					}
				}
				let model = ()=>{
					uni.showModel({
						title:'未授权',
						content: '无法打开摄像机'
					})
				}
				wx.getSetting({
					success(res) {
						if(check(res.authSetting['scope.record'], 'scope.record')&
						check(res.authSetting['scope.camera'], 'scope.camera')&
						check(res.authSetting['scope.writePhotosAlbum'], 'scope.writePhotosAlbum')){
							that.open = true
						}
						else{
							model()
						}
					},
					fail(err){
						console.log(err)
						model()
					}
				})
			},
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
