<template>
	<view class="page">
		<!-- 视频播放 Start -->
		<view class="player">
			<video
			:src="trailerInfo.trailer"
			class="movie"
			:poster="trailerInfo.poster"
			controls=""
			 >
			 </video>
		</view>
		<!-- 视频播放 End -->
		
		<!-- 影片基本信息 Start -->
		<view class="movie-info">
			<image :src="trailerInfo.cover" class="cover"></image>
			<view class="movie-desc">
				<view class="title">{{trailerInfo.name}}</view>
				<view class="basic-info">{{trailerInfo.basicInfo}}</view>
				<view class="basic-info">{{trailerInfo.originalName}}</view>
				<view class="basic-info">{{trailerInfo.totalTime}}</view>
				<view class="basic-info">{{trailerInfo.releaseDate}}</view>
				<view class="score-block">
					<view class="big-score">
						<view class="score-words">综合评分：</view>
						<view class="movie-score">{{trailerInfo.score}}</view>
					</view>
					<view class="score-stars">
						<block class="" v-if="trailerInfo.score >= 0">
							<trailStar :innerScore="trailerInfo.score" showNum="0"></trailStar>
						</block>
						<view class="prise-counts">
							{{trailerInfo.prisedCounts}} 人点赞
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 影片基本信息 End -->
		<view class="line-wapper">
			<view class="line">
				
			</view>
		</view>
		<!-- 剧情介绍 Start -->
		<view class="plot-block">
			<view class="plot-tile">剧情介绍</view>
			<view class="plot-desc">{{trailerInfo.plotDesc}}</view>
		</view>
		<!-- 剧情介绍 End -->
		
		<!-- 剧照 Start -->
		<view class="scroll-block">
			<view class="plot-tile">剧照</view>
			<scroll-view scroll-x="true" class="scroll-list">
				<image :src="item" mode="aspectFill" v-for="(item, index) in picsArr" class="plot-img" @click="preview" :data-current = "index"></image>
			</scroll-view>
		</view>
		<!-- 剧照 End -->
		
		<!-- 演职人员 Start -->
			<view class="scroll-block">
				<view class="plot-tile">演职人员</view>
				<scroll-view scroll-x="true" class="scroll-list">
					<view class="actor-wapper" v-for="(item, index) in directArr">
						<image :src="item.photo" mode="aspectFill" class="single-actor" @click="lookStaff" :data-staffindex="index"></image>
						<view class="actor-name">{{item.name}}</view>
						<view class="actor-role">{{item.actName}}</view>
					</view>
					<view class="actor-wapper" v-for="(item, index) in actorArr">
						<image :src="item.photo" mode="aspectFill" class="single-actor" @click="lookStaff" :data-staffindex="index + directArr.length"></image>
						<view class="actor-name">{{item.name}}</view>
						<view class="actor-role">{{item.actName}}</view>
					</view>
				</scroll-view>
			</view>
		<!-- 演职人员 End -->
	</view>
</template>

<script>
	import trailStar from '../../components/trailerStar.vue' 
	export default {
		data() {
			return{
				trailerInfo: {},
				picsArr: [],
				directArr: [],
				actorArr: []
			}
		},
		onLoad(params) {
			var trailerId = params.trailerId
			this.getMovieDetail(trailerId)
			this.getDirector(trailerId)
			this.getActor(trailerId)
		},
		methods: {
			getMovieDetail(trailerId) {
				uni.request({
					url: this.Base_Url + '/search/trailer/' + trailerId,
					method: 'POST',
					data: {qq:"948845097"},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							this.trailerInfo = res.data.data
							// console.log(this.hotPosterPath)
							this.picsArr = JSON.parse(this.trailerInfo.plotPics)
						}
					},
					fail: () => {}
				});
			},
			getDirector(trailerId) {
				uni.request({
					url: this.Base_Url + '/search/staff/' + trailerId +'/'+'1',
					method: 'POST',
					data: {qq:"948845097",},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							this.directArr = res.data.data
						}
					},
					fail: () => {}
				});
			},
			getActor(trailerId) {
				uni.request({
					url: this.Base_Url + '/search/staff/' + trailerId +'/'+'2',
					method: 'POST',
					data: {qq:"948845097"},
					header:{'Content-type': 'application/x-www-form-urlencoded'},
					success: res => {
						if(res.data.status === 200){
							this.actorArr = res.data.data
						}
					},
					fail: () => {}
				});
			},
			preview(e) {
				let imgIndex = e.currentTarget.dataset.current
				// console.log(imgIndex)
				uni.previewImage({
					urls:this.picsArr,
					current:this.picsArr[imgIndex]
				})
			},
			lookStaff(e){
				let imgIndex = e.currentTarget.dataset.staffindex
				let totalArr = [];
				totalArr = totalArr.concat(this.directArr).concat(this.actorArr)
				let staffUrlArr = []
				for (let i in totalArr) {
					var staffUrl = totalArr[i].photo
					staffUrlArr.push(staffUrl)
				}
				uni.previewImage({
					urls:staffUrlArr,
					current:staffUrlArr[imgIndex]
				})
			}
		},
		components:{
			trailStar
		}
	}
</script>

<style>
	.player{
		display: flex;
		justify-content: center;
		background-color: #000000
	}
	.movie{
		width: 100%;
		height: 440upx;
	}
	.movie-info{
		padding: 40upx 20upx;
		display: flex;
		flex-direction: row;
		background-color: #f7f4f9;
	}
	.cover{
		width: 280upx;
		height: 380upx;
	}
	.movie-desc{
		display: flex;
		flex-direction: column;
		margin-left: 30upx;
		width: 400upx;
	}
	.title{
		font-size: 30px;
	}
	.basic-info{
		color: darkgray;
		font-size: 13px;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
	.score-block{
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		background-color: #fff;
		padding: 20upx;
		margin-top: 20upx;
		width: 360upx;
		height: 90upx;
		box-shadow: 3px 2px 10px #dedede;
	}
	.score-words{
		font-size: 12px;
		color: grey;
	}
	.movie-score{
		font-size: 26px;
		font-weight: bold;
		margin-left: 8upx;
		color: orange;
		line-height: 60upx;
	}
	.prise-counts{
		font-size: 12px;
		color: grey;
		line-height: 44upx;
	}
	.plot-block{
		background-color: #f7f4f9;
		padding: 20upx 40upx;
	}
	.plot-tile{
		color: #A9a9a9;
		font-size: 14px;
	}
	.plot-desc{
		margin-top: 10upx;
		font-size: 16px;
	}
	.scroll-block{
		background-color: #F7F4F9;
		padding: 20upx 40upx;		
	}
	.scroll-list{
		width: 100%;
		white-space: nowrap;
		margin-top: 20upx;
	}
	.plot-img{
		width: 220upx;
		height: 320upx;
		margin-right: 10upx;
	}
	.actor-wapper{
		display: inline-block;
	}
	.single-actor{
		width: 170upx;
		height: 240upx;
		margin-right: 10upx;
	}
	.actor-name{
		width: 170upx;
		text-align: center;
		font-size: 15px;
		white-space: nowrap;
		text-overflow: ellipsis;
		overflow: hidden;
	}
	.actor-role{
		width: 170upx;
		text-align: center;
		font-size: 13px;
		color: #A9A9A9;
		white-space: nowrap;
		text-overflow: ellipsis;
		overflow: hidden;
	}
</style>
