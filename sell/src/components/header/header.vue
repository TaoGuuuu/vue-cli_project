<template>
	<div class="header">
		<div class="content-wrapper">
			<div class="avatar">
				<img :src="seller.avatar" alt="" />
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{seller.name}}</span>
				</div>
				<div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
				<div v-if='seller.supports' class="support">
					<span class="icon" :class="classMap[seller.supports[0].type]"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div v-if="seller.supports" class="support-count" @click="detailShow = true">
				<span class="count">{{seller.supports.length}}个</span>
				<span class="icon-keyboard_arrow_right"></span>
			</div>
		</div>
		<div class="bulletin-wrapper" @click="detailShow = true">
			<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
			<span class="icon-keyboard_arrow_right"></span>
		</div>
		<div class="background">
			<img :src="seller.avatar" alt="" />
		</div>
		<transition name="fade">
			<div class="detail" v-show="detailShow">
				<div class="detail-wrapper clearfix">
					<div class="detail-main">
						<div class="detail-title">
							<h1>{{seller.name}}</h1>
							<div class="star-wrapper">
								<star :size="48" :score="seller.score"></star>
							</div>
						</div>
						<div class="detail-supports">
							<div class="detail-supports-title">
								<div class="line"></div>
								<div class="text">优惠信息</div>
								<div class="line"></div>
							</div>
							<ul>
								<li v-for="item in seller.supports">
									<span class="icon" :class="classMap[item.type]"></span><span class="text">{{item.description}}</span>
								</li>
							</ul>
						</div>
						<div class="detail-bulletin">
							<div class="bulletin-supports-title">
								<div class="line"></div>
								<div class="text">商家公告</div>
								<div class="line"></div>
							</div>
							<p>{{seller.bulletin}}</p>
						</div>
					</div>
				</div>
				<div class="detail-close" @click="detailShow = false">
					<span class="icon-close"></span>
				</div>
			</div>
		</transition>
	</div>
</template>

<script type="text/ecmascript-6">
import star from '../../components/star/star'
export default {
	data() {
		return {
			classMap:[],
			detailShow: false
		}
	},
	props:{
		seller:{
			type:Object
		}
	},
	created(){
		this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
	},
	components:{
		star
	}
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
.header
	position: relative
	overflow: hidden
	color: rgb(255,255,255)
	background: rgba(7,17,27,0.5)
	.content-wrapper
		position: relative
		padding: 24px 12px 18px 24px
		font-size: 0
		.avatar
			display: inline-block
			vertical-align: top
			img
				width: 64px
				heigth: 64px
				border-radius: 2px
		.content
			margin: 2px 0 2px 16px
			display: inline-block
			vertical-align: top
			.title
				.brand
					display: inline-block
					vertical-align: top
					width: 30px
					height: 18px
					bg-image('brand')
					background-size: 30px 18px
					background-repeat: no-repeat
				.name
					margin-left: 6px
					font-size: 16px
					font-weight: bold
					line-height: 18px
			.description
				margin: 8px 0 10px
				font-size: 12px
				line-height: 12px
			.support
				.icon
					display: inline-block
					vertical-align: top
					width: 12px
					height: 12px
					background-size: 12px 12px
					background-repeat: no-repeat
					&.decrease
						bg-image('decrease_1')
					&.discount
						bg-image('discount_1')
					&.guarantee
						bg-image('guarantee_1')
					&.invoice
						bg-image('invoice_1')
					&.special
						bg-image('special_1')	
				.text
					margin-left:4px
					line-height: 12px
					font-size: 10px
		.support-count
			position: absolute
			right: 12px
			bottom: 14px
			padding: 0 8px
			height: 24px
			line-height: 24px
			font-size: 10px
			background: rgba(0,0,0,0.2)
			border-radius: 14px
			text-align: center
			.count
				vertical-align: top
				margin-right: 2px
			.icon-keyboard_arrow_right
				line-height: 24px
	.bulletin-wrapper
		position: relative
		height:28px
		line-height: 28px
		padding: 0 32px 0 12px
		overflow: hidden
		text-overflow: ellipsis
		white-space: nowrap
		background: rgba(7,17,27,0.2)
		.bulletin-title
			display: inline-block
			vertical-align: top
			margin-top: 8px
			bg-image('bulletin')
			width: 22px
			height: 12px
			background-size: 22px 12px
			background-repeat: no-repeat
		.bulletin-text
			vertical-align: top
			font-size: 10px
			margin: 0 4px
		.icon-keyboard_arrow_right
			position: absolute
			right:12px
			line-height: 28px
	.background
		position: absolute
		left: 0
		top: 0
		z-index: -1
		width: 100%
		height: 100%
		filter:blur(10px)
		img
			width: 100%
			height: 100%
	.detail
		&.fade-enter-active,&.fade-leave-active
			transition:all .5s
		&.fade-enter,&.fade-leave-active
			opacity: 0
		position: fixed
		z-index: 100
		top: 0
		left: 0
		width: 100%
		height: 100%
		overflow: auto		
		backdrop-filter:blur(10px)
		background:rgba(7,17,27,0.8)
		.detail-wrapper
			min-height: 100%
			width:100%
			.detail-main
				margin: 64px 36px 0 36px
				padding-bottom: 64px
				.detail-title
					h1
						text-align: center
						font-size: 16px
						font-weight: 700
						line-height: 16px
						margin-bottom: 16px
					.star-wrapper
						margin: 16px 0 28px 0
						text-align: center	
				.detail-supports
					margin: 0 12px 0 12px
					.detail-supports-title
						display: flex
						margin-bottom: 24px
						.line
							flex: 1
							position: relative
							top: -6px
							border-bottom: 1px solid rgba(255,255,255,0.2)
						.text
							font-size: 14px
							line-height: 14px
							font-weight: 700
							padding: 0 12px
							text-align: center							
					li
						margin-bottom: 12px
						&:last-child
							margin-bottom: 28px
						.icon
							display:inline-block
							vertical-align: middle
							width: 16px
							height: 16px
							background-size: 16px 16px
							background-repeat: no-repeat
							&.decrease
								bg-image('decrease_1')
							&.discount
								bg-image('discount_1')
							&.guarantee
								bg-image('guarantee_1')
							&.invoice
								bg-image('invoice_1')
							&.special
								bg-image('special_1')
						.text
							margin-left: 6px
							vertical-align: middle
							font-size: 12px
							line-height: 12px
				.detail-bulletin
					p
						padding: 0 12px
						font-size: 12px
						line-height: 24px
					.bulletin-supports-title
						display: flex
						margin-bottom: 24px
						.line
							flex: 1
							position: relative
							top: -6px
							border-bottom: 1px solid rgba(255,255,255,0.2)
						.text
							font-size: 14px
							line-height: 14px
							font-weight: 700
							padding: 0 12px
							text-align: center								
		.detail-close
			position: relative
			width: 32px
			height: 32px
			margin: -64px auto 0 auto
			clear: both
			font-size: 32px
</style>