<template>
	<div class="ratings" ref="ratings">
		<div class="ratings-content">
			<div class="overview">
				<div class="overview-left">
					<h2 class="score">{{seller.score}}</h2>
					<p class="title">综合评分</p>
					<p class="rank">高于周边商家{{seller.rankRate}}%</p>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<span class="text">服务态度</span>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class="text">服务态度</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="deliveryTime">
						<span class="text">送达时间</span>
						<span class="time">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<split></split>
			<ratingselect :ratings="ratings" :select-type="selectType" :only-content="onlyContent" :desc="desc" @select="selectRating" @toggle="toggleContent"></ratingselect>
			<div class="ratings-wrapper">
				<ul>
					<li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
						<img :src="rating.avatar" class="avatar">
						<div class="rating-content">
							<div class="name">{{rating.username}}</div>
							<div class="star-wrapper">
								<star :size="24" :score="rating.score"></star>
								<span v-show="rating.deliveryTime" class="time">{{rating.deliveryTime}}分钟送达</span>
							</div>
							<p class="text">{{rating.text}}</p>
							<div class="item-bottom" v-show="rating.recommend.length">
								<span :class="{'icon-thumb_up':rating.rateType===0}"></span>
								<li class="recommend" v-for="item in rating.recommend">{{item}}</li>
							</div>
						</div>
						<div class="rateTime">
							{{rating.rateTime | formatDate}}
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script>
import BScroll from 'better-scroll'
import {formatDate} from '../../common/js/date'
import star from '../../components/star/star'
import split from '../../components/split/split'
import ratingselect from '../ratingselect/ratingselect.vue'
const ALL = 2;
const ERR_OK = 0
export default {
	props: {
		seller: {
			type: Object
		}
	},
	data() {
		return {
			selectType: ALL,
			onlyContent: false,
			desc: {
				all: '全部',
				positive: '满意',
				negative: '不满意'
			},
			ratings: []
		}
	},
	created() {
		this.$http.get('/api/ratings').then(response => {
		    response = response.body;
		    if (response.errno === ERR_OK){
		    	this.ratings = response.data;
		    	this.$nextTick(() => {
    			if (!this.scroll) {
     				this.scroll = new BScroll(this.$refs.ratings, {
        				click: true
     		 		});
    			} else {
      				this.scroll.refresh();
           		}
          	});
		    }
		})
	},
	methods: {
	    needShow(type, text) {
	    	if (this.onlyContent && !text) {
	    		return false
	    	}
	    	if (this.selectType === ALL) {
	    		return true
	    	} else {
	    		return type === this.selectType
	    	}
	    },
	    selectRating(type) {
	    	this.selectType = type;
	    	this.$nextTick(() => {
	          this.scroll.refresh();
	        })
	    },
	    toggleContent(onlyContent) {
	    	this.onlyContent = !this.onlyContent;
	    	this.$nextTick(() => {
	          this.scroll.refresh();
	        })
	    }
	},
	filters: {
		formatDate(time) {
		    let date = new Date(time);
		    return formatDate(date, 'yyyy-MM-dd hh:mm');
	    }
	},
	components:{
		star,
		split,
		ratingselect
	}
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
.ratings
	position: absolute
	top: 174px
	bottom: 0
	left: 0
	width: 100%
	overflow: hidden
	.overview
		display: flex
		padding: 18px 0
		.overview-left
			flex: 0 0 137px
			width: 137px
			border-right: 1px solid rgba(7,17,27,0.2)
			text-align: center
			padding: 6px 0
			@media only screen and (max-width: 320px)
				flex: 0 0 115px
				width: 115px
			.score
				font-size: 24px
				color: rgb(255,153,0)
				line-height: 28px
				margin-bottom: 6px
			.title
				font-size: 12px
				color: rgb(7,17,27)
				line-height: 12px
				margin-bottom: 8px
			.rank
				font-size: 10px
				color: rgb(147,153,159)
				line-height: 10px
		.overview-right
			flex: 1
			padding: 6px 24px
			@media only screen and (max-width: 320px)
				padding: 6px
			.score-wrapper
				margin-bottom: 8px
				font-size: 0
				.text
					display: inline-block
					vertical-align: top
					font-size: 12px
					line-height: 18px
					color: rgb(7,17,27)
					margin-right: 12px
				.star
					display: inline-block
					vertical-align: top
					margin-right: 12px
				.score
					display: inline-block
					vertical-align: top
					line-height: 18px
					font-size: 12px
					color: rgb(255,153,0)
			.deliveryTime
				font-size: 0
				.text
					font-size: 12px
					color: rgb(7,17,27)
					line-height: 18px
					margin-right: 12px
				.time
					font-size: 12px
					color: rgb(147,153,159)
					line-height: 18px
	.ratings-wrapper
		padding: 0 18px
		.rating-item
			padding: 18px 0
			position: relative
			display: flex
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.avatar
				display: inline-block
				vertical-align: top
				flex: 0 0 28px
				width: 28px
				height: 28px
				border-radius: 50%
				margin-right: 12px
			.rating-content
				display: inline-block
				vertical-align: top
				flex: 1
				.name
					font-size: 10px
					color: rgb(7,17,27)
					line-height: 12px
					margin-bottom: 4px
				.star-wrapper
					margin-bottom: 6px
					font-size: 0
					.star
						display: inline-block
						vertical-align: top
						margin-right: 6px
					.time
						display: inline-block
						vertical-align: top
						font-size: 10px
						color: rgb(147,153,159)
						line-height: 12px
				.text
					font-size: 12px
					color: rgb(7,17,27)
					line-height: 18px
					margin-bottom: 8px
				.item-bottom
					font-size: 0
					.icon-thumb_up
						display: inline-block
						vertical-align: top
						font-size: 12px
						line-height: 16px
						margin-right: 8px
						color: rgb(0,160,220)
					.recommend
						display: inline-block
						vertical-align: top
						padding: 0 6px
						margin: 0 8px 5px 0
						border: 1px rgba(7,17,27,0.1) solid
						background: #fff
						border-radius: 2px
						font-size: 9px
						color: rgb(147,153,159)
						line-height: 16px
						overflow: hidden
						text-overflow: ellipsis
						white-space: nowrap
						width: 40px
						text-align: center
			.rateTime
				position: absolute
				right: 0
				top: 18px
				font-size: 10px
				color: rgb(147,153,159)
				line-height: 12px
</style>