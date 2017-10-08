<template>
	<div class="seller" ref="seller">
		<div class="seller-content">
			<div class="overview">
				<div class="overview-top">
					<h2 class="name">{{seller.name}}</h2>
					<div class="star-wrapper">
						<star :size="36" :score="seller.score"></star>
						<span class="ratingCount">({{seller.ratingCount}})</span>
						<span class="sellCount">月售{{seller.sellCount}}单</span>
					</div>
					<div class="collect" @click="toggleCollect">
						<span class="icon-favorite" :class="{active:collect}"></span>
						<p v-show="!this.collect" class="text">收藏</p>
						<p v-show="this.collect" class="text">已收藏</p>
					</div>
				</div>
				<div class="overview-bottom">
					<div class="bottom-item">
						<div class="title">起送价</div>
						<div class="content">
							<span class="number">{{seller.minPrice}}</span>
							<span class="text">元</span>
						</div>
					</div>
					<div class="bottom-item">
						<div class="title">商家配送</div>
						<div class="content">
							<span class="number">{{seller.deliveryPrice}}</span>
							<span class="text">元</span>
						</div>
					</div>
					<div class="bottom-item">
						<div class="title">平均配送时间</div>
						<div class="content">
							<span class="number">{{seller.deliveryTime}}</span>
							<span class="text">分钟</span>
						</div>
					</div>
				</div>
			</div>
			<split></split>
			<div class="bulletin-supports">
				<div class="bulletin">
					<h2 class="title">公告与活动</h2>
					<p class="text">{{seller.bulletin}}</p>
				</div>
				<div class="supports">
					<ul>
						<li v-for="item in seller.supports" class="support-item">
							<span class="icon" :class="classMap[item.type]"></span><span class="text">{{item.description}}</span>
						</li>
					</ul>
				</div>
			</div>
			<split></split>
			<div class="seller-pics">
				<h2 class="title">商家实景</h2>
				<div class="pic-wrapper" ref="picWrapper">
					<ul ref="picList">
						<li v-for="pic in seller.pics" class="pic-item">
							<img :src="pic" alt=""/>
						</li>
					</ul>
				</div>
			</div>
			<split></split>
			<div class="seller-infos">
				<h2 class="title">商家信息</h2>
				<ul>
					<li v-for="info in seller.infos" class="info-item">
						<p>{{info}}</p>	
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script>
import BScroll from 'better-scroll'
import star from '../star/star.vue'
import split from '../split/split.vue'
export default {
	props: {
		seller: {
			type: Object
		}
	},
	data() {
		return {
			collect: false
		}
	},
	created(){
		this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
	},
    watch: {
    	'seller'() {
        	this.$nextTick(() => {
	            this._initScroll();
	            this._initPics();
        	});
        }
    },
    mounted() {
      this.$nextTick(() => {
        	this._initScroll();
        	this._initPics();
        });
    },
	methods: {
		toggleCollect() {
			this.collect = !this.collect
		},
		_initScroll() {
	        if (!this.scroll) {
	        	this.scroll = new BScroll(this.$refs.seller, {
	        		click: true
	        	});
	        } else {
	        	this.scroll.refresh();
	        }
	    },
        _initPics() {
        	if (this.seller.pics) {
	            let picWidth = 120;
	            let margin = 6;
	            let width = (picWidth + margin) * this.seller.pics.length - margin;
	            this.$refs.picList.style.width = width + 'px';
	            this.$nextTick(() => {
	            	if (!this.picScroll) {
	                this.picScroll = new BScroll(this.$refs.picWrapper, {
	                	scrollX: true,
	                	eventPassthrough: 'vertical'
	                });
	            } else {
	                this.picScroll.refresh();
	            }
            });
        }
      }
	},
	components: {
		star,
		split
	}
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
.seller
	position: absolute
	top: 174px
	bottom: 0
	left: 0
	width: 100%
	overflow: hidden
	.overview
		padding: 18px
		.overview-top
			position: relative
			padding-bottom: 18px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.name
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
				margin-bottom: 8px
			.star-wrapper
				height: 18px
				font-size: 0
				.star
					display: inline-block
					vertical-align: top
				.sellCount,.ratingCount
					font-size: 10px
					color: rgb(77,85,93)
					line-height: 18px
					&.ratingCount
						margin: 0 12px 0 8px
			.collect
				position: absolute
				top: 0
				right: 0
				width: 35px
				font-size: 24px
				line-height: 24px
				color: #d4d6d9
				text-align: center
				.active
					color: rgb(240,20,20)
				.text
					font-size: 10px
					line-height: 10px
					color: rgb(77,85,93)
					margin-top: 4px
		.overview-bottom
			display: flex
			font-size: 0
			margin-top: 18px
			.bottom-item
				display: inline-block
				flex: 1
				text-align: center
				border-right: 1px solid rgba(7,17,27,0.1)
				&:last-child
					border-right: none
				.title
					font-size: 10px
					color: rgb(147,153,159)
					line-height: 10px
				.content
					margin-top: 4px
					.number
						font-size: 24px
						color: rgba(7,17,27,0.8)
						line-height: 24px
					.text
						font-size: 10px
						color: rgb(7,17,27)
						line-height: 24px
	.bulletin-supports
		padding: 18px 18px 0
		.bulletin
			padding-bottom: 16px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			.title
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
				margin-bottom: 8px
			.text
				padding: 0 12px
				font-size: 12px
				color: rgb(240,20,20)
				line-height: 24px			
		.supports
			.support-item
				padding: 16px 12px
				font-size: 0
				border-bottom: 1px solid rgba(7,17,27,0.1)
				&:last-child
					border-bottom: none
				.icon
					display: inline-block
					vertical-align: top
					width: 16px
					height: 16px
					margin-right: 6px
					background-size: 16px 16px
					background-repeat: no-repeat
					&.decrease
						bg-image('decrease_4')
					&.discount
						bg-image('discount_4')
					&.guarantee
						bg-image('guarantee_4')
					&.invoice
						bg-image('invoice_4')
					&.special
						bg-image('special_4')
				.text
					display: inline-block
					vertical-align: top
					font-size: 12px
					color: rgb(7,17,27)
					line-height: 16px
	.seller-pics
		padding: 18px 0 18px 18px
		.title
			font-size: 14px
			color: rgb(7,17,27)
			line-height: 14px
			margin-bottom: 12px
		.pic-wrapper
			width: 100%
			overflow: hidden
			white-space: nowrap
			font-size: 0
			.pic-item
				display: inline-block
				margin-right: 6px
				&:last-child
					margin-right: 0
				img
					width: 120px 
					height: 90px
	.seller-infos
		padding: 18px 18px 0
		.title
			font-size: 14px
			color: rgb(7,17,27)
			line-height: 14px
			padding-bottom: 12px
			border-bottom: 1px solid rgba(7,17,27,0.1)
		.info-item
			padding: 16px 12px
			border-bottom: 1px solid rgba(7,17,27,0.1)
			&:last-child
				border-bottom: none
			p
				font-size: 12px
				color: rgb(7,17,27)
				line-height: 16px
</style>