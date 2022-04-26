<template>
	<view class="content">
		111
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		onLoad(e) {
			console.log('e', e);
			const id = e.id;
			uni.request({
				url: 'https://unidemo.dcloud.net.cn/api/news/36kr' + id,
				method: 'GET',
				data: {},
				success: res => {
					console.log('res info', res);
				},
				fail: () => {},
				complete: () => {}
			});
		},
		methods: {
			
		}
	}
</script>

<style>
.content{
	padding: 10rpx 2%;
	width: 96%;
	flex-wrap: wrap;
}
.title{
	line-height: 2em;
	font-weight: 700;
	font-size: 38rpx;
}
</style>
