<template>
	<view class="scroll">
		scroll
		<mescroll-body ref="mescrollRef" @init="mescrollInit" @down="downCallback" @up="upCallback" :down="downOption" :up="upOption">
				 
				 <view v-for="data in dataList"> 数据列表... </view>
			 </mescroll-body>
	</view>
</template>

<script>
	// 引入mescroll-mixins.js
	import MescrollMixin from "@/uni_modules/mescroll-uni/components/mescroll-uni/mescroll-mixins.js";
export default {
	mixins: [MescrollMixin], // 使用mixin
	// components: {
	// 	MescrollBody
	// },
	data() {
		return {
			mescroll: null, // mescroll实例对象 (此行可删,mixins已默认)
			// 下拉刷新的配置(可选, 绝大部分情况无需配置)
			downOption: {
				
			},
			// 上拉加载的配置(可选, 绝大部分情况无需配置)
			upOption: {
				page: {
					size: 10 // 每页数据的数量,默认10
				},
				noMoreSize: 5, // 配置列表的总数量要大于等于5条才显示'-- END --'的提示
				empty: {
					tip: '暂无相关数据'
				}
			},
			// 列表数据
			dataList: []
		}
	},
	methods: {
		/*mescroll组件初始化的回调,可获取到mescroll对象 (此处可删,mixins已默认)*/
		mescrollInit(mescroll) {
			this.mescroll = mescroll;
		},
		
		/*下拉刷新的回调, 重置列表为第一页 (此处可删,mixins已默认)
		downCallback(){
			this.mescroll.resetUpScroll();
		},
		/*上拉加载的回调*/
		upCallback(page) {
			// 此处可以继续请求其他接口
			// if(page.num == 1){
			// 	// 请求其他接口...
			// }
			
			// 如果希望先请求其他接口,再触发upCallback,可参考以下写法
			// if(!this.isInitxx){
			// 	apiGetxx().then(res=>{
			// 		this.isInitxx = true
			// 		this.mescroll.resetUpScroll() // 重新触发upCallback
			// 	}).catch(()=>{
			// 		this.mescroll.endErr()
			// 	})
			// 	return // 此处return,先获取xx
			// }
			
			uni.request({
				url: 'https://s.geekbang.org/api/gksearch/search',
				method: 'post',
				data: {
					p: page.num,
					q: "web",
					s: 20,
					t: 0
				},
				success: (data) => {
					console.log('search data', data);
					// 接口返回的当前页数据列表 (数组)
					let curPageData = data.xxx; 
					// 接口返回的当前页数据长度 (如列表有26个数据,当前页返回8个,则curPageLen=8)
					let curPageLen = curPageData.length; 
					// 接口返回的总页数 (如列表有26个数据,每页10条,共3页; 则totalPage=3)
					let totalPage = data.xxx; 
					// 接口返回的总数据量(如列表有26个数据,每页10条,共3页; 则totalSize=26)
					let totalSize = data.xxx; 
					// 接口返回的是否有下一页 (true/false)
					let hasNext = data.xxx; 
					
					//设置列表数据
					if(page.num == 1) this.dataList = []; //如果是第一页需手动置空列表
					this.dataList = this.dataList.concat(curPageData); //追加新数据
					
					// 请求成功,隐藏加载状态
					//方法一(推荐): 后台接口有返回列表的总页数 totalPage
					this.mescroll.endByPage(curPageLen, totalPage); 
					
					//方法二(推荐): 后台接口有返回列表的总数据量 totalSize
					//this.mescroll.endBySize(curPageLen, totalSize); 
					
					//方法三(推荐): 您有其他方式知道是否有下一页 hasNext
					//this.mescroll.endSuccess(curPageLen, hasNext); 
					
					//方法四 (不推荐),会存在一个小问题:比如列表共有20条数据,每页加载10条,共2页.
					//如果只根据当前页的数据个数判断,则需翻到第三页才会知道无更多数据
					//如果传了hasNext,则翻到第二页即可显示无更多数据.
					//this.mescroll.endSuccess(curPageLen);
					
					// 如果数据较复杂,可等到渲染完成之后再隐藏下拉加载状态: 如
					// 建议使用setTimeout,因为this.$nextTick某些情况某些机型不触发
					setTimeout(()=>{
						this.mescroll.endSuccess(curPageLen)
					},20)
					
					// 上拉加载的 curPageLen 必传, 否则会一直显示'加载中...':
					// 1. 使配置的noMoreSize 和 empty生效
					// 2. 判断是否有下一页的首要依据: 
					//    当传的值小于page.size时(说明不满页了),则一定会认为无更多数据;
					//    比传入的totalPage, totalSize, hasNext具有更高的判断优先级;
					// 3. 当传的值等于page.size时(满页),才取totalPage, totalSize, hasNext判断是否有下一页
					// 传totalPage, totalSize, hasNext目的是避免方法四描述的小问题
					
					// 提示: 您无需额外维护页码和判断显示空布局,mescroll已自动处理好.
					// 当您发现结果和预期不一样时, 建议再认真检查以上参数是否传正确
				},
				fail: () => {
					//  请求失败,隐藏加载状态
					this.mescroll.endErr()
				}
			})
			
		},
		/*若希望重新加载列表,只需调用此方法即可(内部会自动page.num=1,再主动触发up.callback)*/
		reloadList(){
			this.mescroll.resetUpScroll();
		}
	},
}
</script>

<style>
</style>