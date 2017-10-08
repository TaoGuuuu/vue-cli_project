<template>
	<transition name="move">
		<div class="food" v-show="showFlag" ref="food">
			<div class="food-content">
				<div class="iamge-header">
					<img :src="food.image" alt="" />
					<div class="back" @click="hide">
						<span class="icon-arrow_lift"></span>
					</div>
				</div>
				<div class="food-text">
					<h3 class="food-name">{{food.name}}</h3>
					<div class="food-description">
						<span>月售{{food.sellCount}}份</span><span class="food-rating">好评率{{food.rating}}%</span>
					</div>
					<div class="price">
						<span class="food-price">￥{{food.price}}</span><span class="food-oldPrice" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
					</div>
					<div class="cartcontrol-wrapper" v-show="food.count">
						<cartcontrol :food="food" @add="addFood"></cartcontrol>
					</div>
					<transition name="fold">
						<div v-show="!food.count || food.count===0" class="add-food" @click="selFood">加入购物车</div>
					</transition>
				</div>
				<split v-show="food.info"></split>
				<div class="food-info" v-show="food.info">
					<h3 class="info-title">商品介绍</h3>
					<p class="info-text">{{food.info}}</p>
				</div>
				<split></split>
				<div class="food-ratings">
					<div class="ratings-title">商品评价</div>
					<ratingselect :ratings="food.ratings" :select-type="selectType" :only-content="onlyContent" :desc="desc" @select="selectRating" @toggle="toggleContent"></ratingselect>
					<div class="rating-wrapper">
						<ul v-show="food.ratings && food.ratings.length">
							<li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
								<div class="user">
									<span class="name">{{rating.username}}</span>
									<img :src="rating.avatar" alt="" class="avatar">
								</div>
								<div class="time">{{rating.rateTime | formatDate}}</div>
								<p class="text">
									<span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span><span class="content">{{rating.text}}</span>
								</p>
							</li>
						</ul>
						<div class="no-ratings" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
					</div>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>
import BScroll from 'better-scroll'
import Vue from 'vue'
import {formatDate} from '../../common/js/date'
import cartcontrol from '../cartcontrol/cartcontrol.vue'
import split from '../split/split.vue'
import ratingselect from '../ratingselect/ratingselect.vue'
const ALL = 2;
export default {
	props: {
		food: {
			type: Object
		}
	},
	data() {
		return {
			showFlag: false,
			selectType: ALL,
			onlyContent: false,
			desc: {
				all: '全部',
				positive: '推荐',
				negative: '吐槽'
			}
		}
	},
	methods: {
		show() {
			this.showFlag = true;
			this.selectType = ALL;
			this.onlyContent = false;
  			this.$nextTick(() => {
    			if (!this.scroll) {
     				this.scroll = new BScroll(this.$refs.food, {
        				click: true
     		 		});
    			} else {
      				this.scroll.refresh();
           		}
          	});
		},
		hide() {
			this.showFlag = false
		},
		selFood(event) {
			if (!event._constructed) {
		        	return;
		        }
			if (!this.food.count) {
				Vue.set(this.food, 'count', 1)
			} else {
				this.food.count++;
			}
			this.$emit('add', event.target);
		},
		addFood(target) {
	    	this.$emit('add', target);
	    },
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
	components: {
		cartcontrol,
		split,
		ratingselect
	}
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
.food
	position: fixed
	left: 0
	top: 0
	bottom: 48px
	z-index: 10
	width: 100%
	overflow: hidden
	background: #fff
	&.move-enter-active, &.move-leave-active
			transition: all .5s linear	
	&.move-enter, &.move-leave-to
		opacity: 0
		transform: translateX(100%)
	.food-content
		.iamge-header
			position: relative
			width: 100%
			height: 0
			padding-top: 100%
			img
				position: absolute
				top: 0
				left: 0
				width: 100%
				height: 100%
			.back
				position: absolute
				top: 10px
				left: 0
				.icon-arrow_lift
					display: block
					padding: 18px
					font-size: 16px
					color: #fff
		.food-text
			position: relative
			padding: 18px
			.food-name
				font-size: 14px
				font-weight: 700
				color: rgb(7,17,27)
				line-height: 14px
				margin-bottom: 8px
			.food-description
				font-size: 10px
				color: rgb(147,153,159)
				line-height: 10px
				margin-bottom: 18px
				.food-rating
					margin-left: 12px
			.price
				line-height: 24px		
				.food-price
					color: rgb(240,20,20)
					font-size: 14px
					font-weight: 700
				.food-oldPrice
					color: rgb(147,153,159)
					font-size: 10px
					font-weight: normal
					margin-left: 8px
					text-decoration:line-through
			.add-food
				position: absolute
				right: 18px
				bottom: 18px
				width: 74px
				height: 24px
				font-size: 10px
				line-height: 24px
				text-align: center
				border-radius: 24px
				color: #fff
				background: rgb(0,160,220)
				&.fold-enter-active, &.fold-leave-active
						transition: all 0.3s linear	
				&.fold-enter, &.fold-leave-to
					opacity: 0
			.cartcontrol-wrapper
				position: absolute
				right: 18px
				bottom: 18px
				margin-bottom: -3px;
		.food-info
			padding: 18px
			.info-title
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
				margin-bottom: 6px
			.info-text
				padding: 0 8px
				font-size: 12px
				color: rgb(77,85,93)
			line-height: 24px
		.food-ratings
			padding-top: 18px
			.ratings-title
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
				margin-left: 18px
			.rating-wrapper
				padding: 0 18px
				.rating-item
					position: relative
					padding: 16px 0
					border-bottom: 1px solid rgba(7,17,27,0.1)
					.user
						font-size: 0
						position: absolute
						right: 0
						top: 16px
						.name
							display: inline-block
							vertical-align: top
							font-size: 10px
							color: rgb(147,153,159)
							line-height: 12px
						.avatar
							display: inline-block
							vertical-align: top
							width: 12px
							height: 12px
							border-radius: 50%
							margin-left: 6px
					.time
						margin-bottom: 6px
						font-size: 10px
						color: rgb(147,153,159)
						line-height: 12px
					.text
						font-size: 12px
						.icon-thumb_up,.icon-thumb_down
							line-height: 16px
							margin-right: 4px
							&.icon-thumb_up
								color: rgb(0,160,220)
							&.icon-thumb_down
								color: rgb(147,153,159)
						.content
							color: rgb(7,17,27)
							line-height: 16px
				.no-ratings
					padding: 16px 0
					font-size: 12px
					color: rgb(147,153,159)
</style>