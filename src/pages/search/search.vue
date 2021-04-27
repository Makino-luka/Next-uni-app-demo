<template>
	<view class="page">
		<view class="search-block">
			<view class="search-ico-wapper">
				<image src="../../static/搜索.png" mode="" class="search-ico"></image>
			</view>
			<input type="text" value="" class="input-text" focus="true" v-model="toSearch" @input="debounce"/>
		</view>
		
		<view class="movie-list page-block">
			<view class="movie-wapper" v-for="(item,index) in searchList">
				<navigator :url="'../detail/detail?trailerId=' + item.id">
					<image :src="item.cover" mode="" class="poster"></image>
				</navigator>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchList: [],
				toSearch:'',
				timeout:'',
				page:1,
				totalPage:''
			}
		},
		onLoad() {
			this.getSearchList()
		},
		onReachBottom() {
			if(this.page < this.totalPage){
				this.page ++
				this.getSearchList(1)
			}
		},
		methods: {
			getSearchList(v) {
				uni.showLoading({
					mask:true
				})
				// 判断事件类型为 查询：0 则初始化分页
				if(v === 0){
					this.page = 1
				}
				let data = {
					qq:948845097,
					keywords:this.toSearch,
					page:this.page,
					pageSize:15
				}
				uni.request({
					url: this.Base_Url + '/search/list',
					method: 'POST',
					data: data,
					header: {'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							// 判断事件类型为 查询：0 还是 下拉：1，实现不同的展示效果
							if(v===0){
								this.searchList = res.data.data.rows
							} else {
								this.searchList.push(...res.data.data.rows)
							}
							this.page = res.data.data.page
							// console.log(this.searchList)
							this.totalPage = res.data.data.total
						}
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading()
					}
				});
			},
			// 构建防抖函数
			debounce(){
				let that = this
				if(this.timeout){
					clearTimeout(this.timeout)
				}
				this.timeout = setTimeout(()=>{
					that.getSearchList(0)
				},500)
			}
		}
	}
</script>

<style>
.search-block{
	display: flex;
	flex-direction: row;
	padding: 0upx 20upx 20upx 20upx;
	/* border-radius: 20px; */
	/* background-color: #fff */
}
.search-ico-wapper{
	display: flex;
	flex-direction: column;
	justify-content: center;
	background-color: #eaeaea;
	padding: 0upx 10upx;
}
.search-ico{
	width: 40upx;
	height: 40upx;
	align-self: center;
}
.input-text{
	font-size: 20px;
	width: 650upx;
	height: 60upx;
	background-color: #eaeaea;
}
.movie-list{
	display: flex;
	flex-direction: row;
	justify-content: flex-start;
	flex-wrap: wrap;
	padding: 10upx 10upx 0 10upx;
}
.movie-wapper{
	padding: 10upx 20upx;
}
.poster{
		width: 200upx;
		height: 270upx;
	}
</style>
