<template>
	<view class="page">
		<!-- 轮播图 Start -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" class="carousel" indicator-color="rgba(255,255,255,1)" indicator-active-color="rgba(20,20,20,0.3)">
			<swiper-item v-for="(item) in carouselPath">
				<navigator open-type="navigate" :url="'../detail/detail?trailerId=' + item.movieId">
					<image :src="item.image" mode=""></image>
				</navigator>
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
					<navigator open-type="navigate" :url="'../detail/detail?trailerId=' + item.id">
						<image :src="item.cover" mode="" class="poster"></image>
					</navigator>
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
		
		<view class="guess-u-like page-block">
			<view class="single-like-movie" v-for="(item,gindex) in guessULikeList">
				<navigator open-type="navigate" :url="'../detail/detail?trailerId=' + item.id">
					<image :src="item.cover" mode="" class="like-movie"></image>
				</navigator>
				<view class="movie-desc">
					<view class="movie-title">
						{{item.name}}
					</view>
					
					<trail-star :innerScore = "item.score" showNumber = "0"></trail-star>
					
					<view class="movie-info">
						{{item.basicInfo}}
					</view>
					
					<view class="movie-info">
						{{item.releaseDate}}
					</view>
				</view>
				
				<view class="movie-oper" @click="praiseMe" :data-gindex="gindex">
					<image src="../../static/admit.png" mode="" class="praise-ico"></image>
					<view class="praise-me">
						点赞
					</view>
					<view class="praise-me animation-opacity" :animation="animationDataArr[gindex]">
						+1
					</view>
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
				trailerPath:[],
				guessULikeList:[],
				animationData:{},
				animationDataArr:[]
			}
		},
		onLoad() {
			this.refresh()
			// 在页面创建时创建一个临时动画
			this.animation = uni.createAnimation()
		
		},
		onUnload() {
			this.animationData = {}
			this.animationDataArr = []
		},
		onPullDownRefresh() {
			uni.showLoading({
				mask:true
			})
			this.refresh()
			// 在页面创建时创建一个临时动画
			this.animation = uni.createAnimation()
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
			},
			getGuessULike() {
				uni.request({
					url: this.Base_Url + '/index/guessULike',
					method: 'POST',
					data: {qq:"948845097"},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							this.guessULikeList = res.data.data
						}
					},
					fail: () => {},
					complete: () => {
						uni.stopPullDownRefresh()
						uni.hideLoading()
					}
				});
			},
			refresh() {
				this.getCarousel()
				this.getHotPoster()
				this.getPreviewVideo()
				this.getGuessULike()
			},
			praiseMe (e) {
				var gindex = e.currentTarget.dataset.gindex;
				this.animation.translateY(-70).opacity(1).step({
					duration:300
				});
				// this.animationData = this.animation.export();
				// this.animationDataArr[gindex] = this.animationData
				this.$set(this.animationDataArr,gindex,this.animation.export())
				setTimeout(function() {
					this.animation.translateY(0).opacity(0).step({
						duration:0
					})
					// this.animationData = this.animation.export();
					// this.animationDataArr[gindex] = this.animationData
					this.$set(this.animationDataArr,gindex,this.animation.export())
				}.bind(this),400)
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
	.guess-u-like{
		display: flex;
		flex-direction: column;
	}
	.single-like-movie{
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		padding: 30upx 20upx;
	}
	.like-movie{
		width: 180upx;
		height: 240upx;
		border-radius: 3%;
	}
	.movie-desc{
		display: flex;
		flex-direction: column;
		width: 320upx;
	}
	.movie-title{
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
	.movie-info{
		color: #808080;
		font-size: 14px;
	}
	/* 点赞css */
	.movie-oper{
		width: 140upx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		border-left: dashed 2px #F1F1F1;
	}
	.movie-oper:active{
		background: #F8F8F8;
		/* opacity: .4; */
		/* box-shadow: #C0C0C0; */
	}
	.praise-ico{
		width: 60upx;
		height: 60upx;
		align-self: center;
	}
	.praise-ico:active{
		background: #F8F8F8;
		opacity: .4;
		/* box-shadow: #C0C0C0; */
	}
	.praise-me{
		font-size: 14px;
		color:#a9d9ff;
		align-self:center;
	}
	.animation-opacity{
		font-weight: bold;
		opacity: 0;
	}
</style>
