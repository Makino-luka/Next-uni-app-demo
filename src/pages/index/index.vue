<template>
	<view class="page">
		<!-- 轮播图 Start -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" class="carousel" indicator-color="rgba(255,255,255,1)" indicator-active-color="rgba(20,20,20,0.3)">
			<swiper-item v-for="(item) in carouselPath">
				<image :src="item.image" mode=""></image>
			</swiper-item>
		</swiper>
		<!-- 轮播图 End -->
		
		<!-- 滑动热点 Start -->
		 <view class="page-block super-hot">
		 	<view class="hot-title-wapper">
		 		<image src="../../static/扭蛋.png" mode="" class="hot-ico"></image>
				<view class="hot-title">
					热门超英
				</view>
		 	</view>
		 </view>
		 
		 <scroll-view scroll-x="true" class="page-block hot">
		 	<view class="single-poster" v-for="(item) in hotPosterPath">
				<view class="poster-wapper">
					<image :src="item.cover" mode="" class="poster"></image>
					<view class="movie-name">
						{{item.name}}
					</view>
					<trail-star :innerScore = "item.score" showNumber = "1"></trail-star>
				</view>
			</view>
		</scroll-view>
		<!-- 滑动热点 End -->
		
		<!-- 热门预告 Start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/相机.png" mode="" class="hot-ico"></image>
				<view class="hot-title">
					热门预告
				</view>
			</view>
		</view>
		
		<view class="hot-movie page-block">
			<video
			v-for="(item) in trailerPath"
			:src="item.trailer"
			class="hot-video"
			:poster="item.poster"
			 >
			 </video>
		</view>
		<!-- 热门预告 End -->
		
		<!-- 猜你喜欢 Start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/小熊.png" mode="" class="hot-ico"></image>
				<view class="hot-title">
					猜你喜欢
				</view>
			</view>
		</view>
		<!-- 猜你喜欢 End -->
	</view>
</template>

<script>
	import trailStar from "../../components/trailerStar.vue"
	export default {
		data() {
			return {
				title: '这里是首页',
				carouselPath:[],
				hotPosterPath:[],
				trailerPath:[]
			}
		},
		async onLoad() {
			this.getCarousel()
			this.getHotPoster()
			this.getPreviewVideo()
		},
		methods: {
			getCarousel() {
				// let data = {qq:948845097}
				uni.request({
					url: this.Base_Url + "/index/carousel/list",
					method:"POST",
					data:{qq:948845097},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: (res) => {
						// console.log(res)
						// {data} = res
						if(res.data.status === 200){
							this.carouselPath = res.data.data
							console.log(this.carouselPath)
						}
					},
					fail: (res) => {
						console.log(res)
					}
				})
			},
			getHotPoster() {
				uni.request({
					url: this.Base_Url + '/index/movie/hot',
					method: 'POST',
					data: {type: "superhero",qq:"948845097"},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							this.hotPosterPath = res.data.data
						}
					},
					fail: () => {}
				});
			},
			getPreviewVideo() {
				uni.request({
					url: this.Base_Url + '/index/movie/hot',
					method: 'POST',
					data: {type: "trailer",qq:"948845097"},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							this.trailerPath = res.data.data
						}
					},
					fail: () => {}
				});
			}
		},
		components:{
			trailStar
		}
	}
</script>

<style>
	.carousel {
		width: 100%;
		height: 440rpx;
	}
	.carousel image{
		width: 100%;
	}
	.super-hot{
		margin-top: 12upx;
		padding: 20upx;
	}
	.hot-title-wapper{
		display: flex;
		flex-direction: row;
	}
	.hot-ico{
		width: 30upx;
		height: 30upx;
		margin-top: 15upx;
	}
	.hot-title{
		font-size: 20px;
		margin-left: 20upx;
		font-weight: bold;
	}
	.hot{
		width: 100%;
	
		white-space: nowrap;
	}
	.single-poster{
		display: inline-block;
		margin-left: 20upx;
	}
	.poster-wapper{
		display: flex;
		flex-direction: column;
	}
	.poster{
		width: 200upx;
		height: 270upx;
	}
	.movie-name{
		width: 200upx;
		margin-top: 10upx;
		/* text-align: center; */
		font-size: 14px;
		font-weight: bold;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
	/* 热门预告 */
	.hot-movie{
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: space-between;
		padding: 0upx 20upx 20upx 20upx;
	}
	.hot-video{
		width: 350upx;
		height: 220upx;
		margin-top: 10upx;
	}
</style>
