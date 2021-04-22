<template>
	<view class="movie-score-wapper">
		<image src="../static/点赞.png" mode="" class="star-ico" v-if="yellowScore !== 0" v-for="yellow in yellowScore"></image>
		<image src="../static/admit-no.png" mode="" class="star-ico" v-if="grayScore !== 0" v-for="gray in grayScore"></image>
		<view class="movie-score" v-if="showNumber === '1'">
			{{innerScore}}
		</view>
	</view>
</template>

<script>
	export default {
		name:"trailerStar",
		data() {
			return {
				yellowScore:0,
				grayScore:5
			};
		},
		props:{
			innerScore: 0,
			showNumber: 0
		},
		created() {
			let tempScore = 0
			if(this.innerScore){
				tempScore = this.innerScore
				this.yellowScore = parseInt(tempScore/2)
				this.grayScore = 5 - this.yellowScore
			}
		}
	}
</script>

<style>
	.movie-score-wapper {
		display: flex;
		flex-direction: row;
	}
	.star-ico{
		width: 20upx;
		height: 20upx;
		margin-top: 6upx;
	}
	.movie-score{
		font-size: 12px;
		color:#C0C0C0;
		margin-left: 8upx;
	}
</style>
