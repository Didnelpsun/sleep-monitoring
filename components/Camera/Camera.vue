<template>
	<view class="c">
		<view class="topView r">
			<image class="topButton" src="/static/image/home.png" @click="open=!open"></image>
			<image class="topButton" src="/static/image/resize.png" @click="resize"></image>
		</view>
		<camera style="height: 100vh;width: 100vw;" :flash="flashState"
		 :device-position="deviceState" :frame-size="frame"
		  :binderror="error" :bindstop="stop"></camera>
		 <view class="bottomView c">
			<text style="color: white;margin-bottom: 10px;">{{timeString}}</text> 
			<view class="buttonView r">
				<image class="cameraImg" :src="flashImg" @click="flash"></image>
				<view class="monitorView c" @click="monitor">
					<button :style="monitorButton"></button>
				</view>
				<image class="cameraImg" src="/static/image/switch.png" @click="device"></image>
			</view>
		 </view>
	</view>
</template>

<script>
	import { postVideo } from '../../api/api.js'
	export default {
		data() {
			return {
				timeString: '00:00:00',
				// time: 0,
				interval: null,
				timeout: null,
				frame: 'small',
				cameraState: false,
				flashState: 'off',
				flashImg: '/static/image/flash_off.png',
				deviceState: 'front',
			};
		},
		methods: {
			error(){
				wx.showModal({
					title:'权限限制',
					content:'您的相机未给小程序授权，无法打开相机'
				})
			},
			stop(){
				
			},
			resize(){
				switch (this.frame) {
					case 'small':
						this.frame = 'medium'
						wx.showModal({
							title:'帧数据尺寸',
							content:'已切换到中等'
						})
						break
					case 'medium':
						this.frame = 'large'
						wx.showModal({
							title:'帧数据尺寸',
							content:'已切换到较大'
						})
						break
					case 'large':
						this.frame = 'small'
						wx.showModal({
							title:'帧数据尺寸',
							content:'已切换到较小'
						})
						break
				}
			},
			monitor(){
				// 点击控制
				// console.log(this.cameraState)
				this.cameraState = !this.cameraState
				if(this.cameraState === true){
					this.intervalSet()
				}
				else{
					this.clear()
				}
			},
			intervalSet(){
				let space = 1000
				let camera = wx.createCameraContext()
				let that = this
				camera.startRecord({
					success: (res) => {
						console.log('开启成功')
						loop()
					},
					fail: (error) => {
						console.log('开启失败')
						console.log(error)
						that.clear()
					}
				})
				let loop=()=>{
					if(that.cameraState === true){
						that.timeout = setTimeout(
							()=>{
								that.interval = setInterval(()=>{
									camera.stopRecord({
										success: (res) => {
											// 相机关闭就开始传输
											that.post(res)
											console.log(res)
										},
										fail: (error) => {
											console.log('结束失败')
											console.log(error)
											that.clear()
										}
									})
									camera.startRecord({
										success: (res) => {
											// console.log('开启成功')
											that.intervalSet()
											// console.log(res)
										},
										fail: (error) => {
											console.log('开启失败')
											console.log(error)
											that.clear()
										},
										timeoutCallback: (res) => {
											console.log('超时')
											that.clear()
										}
									})
									that.timeAdd(that.timeString)
								},space)
							}
						,space)
					}else{
						this.clear()
					}
				}
			},
			post(obj){
				let that = this
				// console.log(postVideo)
				uni.request({
					url: postVideo,
					method: 'POST',
					data: {
						tempVideoPath: obj.tempVideoPath
					},
					success(res){
						if(res.data!=='success')
							that.clear()
					},
					fail() {
						console.log('传输错误')
						try{
							that.$nextTick(function(){
								that.clear()
							})
						}
						catch(error){
							console.log(error)
						}
					}
				})
			},
			timeAdd(){
				let [hour,minute,second] = this.timeString.split(':')
				hour = parseInt(hour)
				minute = parseInt(minute)
				second = parseInt(second)
				//当秒数需要进位
				if(second === 59){
					//当分钟需要进位
					if(minute === 59){
						minute = 0
						hour++
					}
					else{
						minute++
					}
					second = 0
				}
				//不需要任何进位
				else{
					second++
				}
				let two = function(n){
					if(n<10)
						return "0"+n.toString()
					else
						return n.toString()
				}
				this.timeString = two(hour)+":"+two(minute)+":"+two(second)
			},
			clear(){
				clearInterval(this.interval)
				this.interval = null
				this.cameraState = false
				this.timeString = '00:00:00'
				console.log('结束')
			},
			flash(){
				switch (this.flashState) {
					case 'off':
						this.flashState = 'on'
						this.flashImg = '/static/image/flash_on.png'
						break
					case 'on':
						this.flashState = 'auto'
						this.flashImg = '/static/image/flash_auto.png'
						break
					case 'auto':
						this.flashState = 'torch'
						this.flashImg = '/static/image/flash_torch.png'
						break
					case 'torch':
						this.flashState = 'off'
						this.flashImg = '/static/image/flash_off.png'
						break
				}
				// console.log(this.flashState)
			},
			device(){
				this.deviceState = this.deviceState === 'front'?'back':'front'
				// console.log(this.deviceState)
			}
		},
		computed:{
			monitorButton: function(){
				if(this.cameraState === true){
					return "background-color: red;width: 10vw;height: 10vw"
				}
				else if(this.cameraState === false){
					return "background-color: red;width: 15vw;height: 15vw;border-radius: 50%"
				}
			}
		}
	}
</script>

<style lang="scss">
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
	.topView{
		position: fixed;
		top: 0;
		background-color: rgba(0,0,0,0.4);
		// height: 15vw;
		width: 100vw;
		z-index: 5;
	}
	.topButton{
		width: 10vw;
		height: 10vw;
		border-radius: 50%;
		margin: 10px;
	}
	.bottomView{
		position: fixed;
		bottom: 0;
	}
	.buttonView{
		width: 100vw;
		background-color: rgba(0,0,0,0.4);
	}
	.cameraImg{
		width: 12vw;
		height: 12vw;
		border-radius: 50%;
		margin: 20px;
	}
	.monitorView{
		background-color: rgba(0,0,0,0);
		width: 15vw;
		height: 15vw;
		border-radius: 50%;
		border-style: solid;
		border-width: 1vw;
		border-color: white;
	}
</style>
