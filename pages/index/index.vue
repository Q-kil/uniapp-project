<template>
	<view class="content">
		<view class="">
			test
		</view>
		<uni-list>
			<uni-list-item v-for="(item,index) in news" :title="item.title" :note="item.created_at" name="" :thumb="item.author_avatar"
			:clickable="true" @click="openInfo(item.post_id)">
			</uni-list-item>
		</uni-list>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				news: []
			}
		},
		onLoad() {
			uni.request({
				url: 'https://unidemo.dcloud.net.cn/api/news',
				method: 'GET',
				data: {},
				success: res => {
					console.log('res', res);
					this.news = res.data;
				},
				fail: () => {},
				complete: () => {}
			});
			// localStorage.setItem('test', JSON.stringify({a:1}));
			uni.setStorage({
				key: 'key',
				data: '{a: 1}'
			})
		},
		methods: {
			openInfo(id){
				console.log('open', id);
				uni.navigateTo({
					url: '../info/info?id=' + id
				});
			}
		}
	}
</script>

<style>
</style>
