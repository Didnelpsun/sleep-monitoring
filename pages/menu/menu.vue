<template>
	<view>
		<view class="user">
			<view>
				<image class="userimg" :src="user.userimg" v-if="user.userimg.trim()!==''"></image>
				<image class="userimg" src="../../static/image/user.png" v-else-if="user.userimg.trim()===''"></image>
			</view>
			<view class="usertext">
				<text>{{user.userid}}</text>
				<text v-if="user.username.trim()===''">未设置用户名</text>
				<text v-if="user.username.trim()!==''">{{user.username}}</text>
			</view>
		</view>
		<Card>
			<template v-slot:title>本次监督时长</template>
			<template v-slot:value>{{time}}分钟</template>
		</Card>
		<Card>
			<template v-slot:title>眨眼次数</template>
			<template v-slot:value>{{blink}}次</template>
		</Card>
		<Card>
			<template v-slot:title>最大闭眼时长</template>
			<template v-slot:value>{{closeTime}}分钟</template>
		</Card>
		<Card>
			<template v-slot:title>打哈欠次数</template>
			<template v-slot:value>{{yawn}}次</template>
		</Card>
		<Card>
			<template v-slot:title>点头次数</template>
			<template v-slot:value>{{nod}}次</template>
		</Card>
		<Card>
			<template v-slot:title>预计瞌睡时长</template>
			<template v-slot:value>{{sleepyTime}}分钟</template>
		</Card>
	</view>
</template>

<script>
	import Card from '../../components/Card/Card.vue'
	export default {
		data() {
			return {
				user:{
					username:'',
					userid: '',
					userimg: ''
				},
				time: 0, // 时长
				blink: 0, // 眨眼
				closeTime: 0, //闭眼
				yawn: 0, // 打哈欠
				nod: 0, //点头
				sleepyTime: 0
			};
		},
		components:{
			Card
		}
	}
</script>

<style lang="scss" scoped>
	.user{
		background-color: $uni-bg-color;
		margin: 10px 20px 10px;
		padding: 10px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
		font-size: $uni-font-size-base;
		color: $uni-text-color;
	}
	.userimg{
		width: 100rpx;
		height: 100rpx;
	}
	.usertext{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
</style>
