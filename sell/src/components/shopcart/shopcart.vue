<template>
	<div>
		<div class="shopcart">
			<div class="content" @click="toggleList">
				<div class="content-left">
					<div class="logo-wrapper">
						<div class="logo" :class="{'highLight': totalCount}">
							<span class="icon-shopping_cart" :class="{'highLight': totalCount}"></span>
						</div>
						<div class="num" v-show="totalCount">
							{{totalCount}}
						</div>
					</div>
					<div class="price" :class="{'highLight': totalPrice}">￥{{totalPrice}}</div>
					<div class="desc">另需配送费￥{{deliveryPrice}}元</div>
				</div>
				<div class="content-right" :class="{'enough': this.totalPrice >= this.minPrice}" @click.stop.prevent="pay">
					<div class="text">{{payDesc}}</div>
				</div>
			</div>
			<div class="ball-container">
				<div v-for="ball in balls">
				<transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
					<div v-show="ball.show" class="ball">
						<div class="inner inner-hook"></div>
					</div>
				</transition>
				</div>
			</div>
			<transition name="fold">
				<div class="shopcart-list" v-show="listShow">
					<div class="list-header">
						<h1 class="title">购物车</h1>
						<span class="empty" @click="empty">清空</span>
					</div>
					<div class="list-content" ref="listContent">
						<ul>
							<li class="food" v-for="food in selectFoods">
								<span class="name">{{food.name}}</span>
								<div class="price">
									<span>￥{{food.price*food.count}}</span>
								</div>
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food"></cartcontrol>
								</div>
							</li>
						</ul>
					</div>
				</div>
			</transition>
		</div>
		<transition name="fade">
	    	<div class="list-mask" @click="hideList" v-show="listShow"></div>
	    </transition>
   </div>
</template>

<script>
	import BScroll from 'better-scroll'
	import cartcontrol from '../cartcontrol/cartcontrol.vue'
	export default {
		props: {
			deliveryPrice: {
				type: Number,
				default: 0
			},
			minPrice: {
				type: Number,
				default: 0
			},
			selectFoods: {
				type: Array,
				default() {
					return []
				}
			}
		},
		data() {
			return {
				balls: [
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					},
					{
						show: false
					}
				],
				dropBalls: [],
				fold: true
			}
		},
		computed: {
			totalPrice() {
				let total = 0;
				this.selectFoods.forEach((food) => {
					total += food.price * food.count
				});
				return total
			},
			totalCount() {
				let counts = 0;
				this.selectFoods.forEach((food) => {
					counts += food.count
				});
				return counts
			},
			payDesc() {
				if (this.totalPrice === 0) {
					return '￥' + this.minPrice + '元起送'
				} else if (this.totalPrice < this.minPrice) {
					return '还差￥' + (this.minPrice - this.totalPrice) + '起送'
				} else {
					return '去结算'
				}
			},
			listShow() {
				if (!this.totalCount) {
					this.fold = true;
					return false
				}
				let show = !this.fold;
				if (show) {
          			this.$nextTick(() => {
            			if (!this.scroll) {
             				this.scroll = new BScroll(this.$refs.listContent, {
                				click: true
             		 		});
            			} else {
              				this.scroll.refresh();
		           		}
		          	});
		        }
				return show
			}
		},
		methods: {
			empty() {
		        this.selectFoods.forEach((food) => {
		        	food.count = 0;
		        });
		    },
		    pay() {
		        if (this.totalPrice < this.minPrice) {
		        	return;
		        }
		        window.alert(`支付${this.totalPrice}元`);
		    },
		    hideList() {
		        this.fold = true;
		    },
			toggleList() {
				if (!this.totalCount) {
					return
				}
				this.fold = !this.fold
			},
			drop(el) {
				for (let i = 0; i < this.balls.length; i++) {
					let ball = this.balls[i];
					if (!ball.show) {
						ball.show = true;
						ball.el = el;
						this.dropBalls.push(ball);
						return
					}
				}
			},
			beforeDrop(el) {
		    	let count = this.balls.length;
		        while (count--) {
		            let ball = this.balls[count];
		            if (ball.show) {
			            let rect = ball.el.getBoundingClientRect();
			            let x = rect.left - 32;
			            let y = -(window.innerHeight - rect.top - 22);
			            el.style.display = '';
			            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
			            el.style.transform = `translate3d(0,${y}px,0)`;
			            let inner = el.getElementsByClassName('inner-hook')[0];
			            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
			            inner.style.transform = `translate3d(${x}px,0,0)`;
		            }
		        }
		    },
		    dropping(el, done) {
		        /* eslint-disable no-unused-vars */
		        let rf = el.offsetHeight;
		        this.$nextTick(() => {
				    el.style.webkitTransform = 'translate3d(0,0,0)';
					el.style.transform = 'translate3d(0,0,0)';
				    let inner = el.getElementsByClassName('inner-hook')[0];
				    inner.style.webkitTransform = 'translate3d(0,0,0)';
				    inner.style.transform = 'translate3d(0,0,0)';
				    el.addEventListener('transitionend', done);
		        });
		    },
		    afterDrop(el) {
		        let ball = this.dropBalls.shift();
		        if (ball) {
		            ball.show = false;
		            el.style.display = 'none';
		        }
		    }
		},
		components: {
			cartcontrol
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
.shopcart
	position: fixed
	left: 0
	bottom: 0
	z-index: 20
	height: 48px
	width: 100%
	.content
		display: flex
		background: #141d27
		.content-left
			flex: 1
			font-size: 0
			.logo-wrapper
				display: inline-block
				position: relative
				top: -10px
				margin: 0 12px
				padding: 6px
				width: 56px
				height: 56px
				box-sizing: border-box
				vertical-align: top
				border-radius: 50%
				background: #141d27
				.logo
					width: 100%
					height: 100%
					text-align: center
					border-radius: 50%
					background: rgba(255,255,255,0.2)
					&.highLight
						background: #00a0dc
					.icon-shopping_cart
						font-size: 24px
						color: #80858a
						line-height: 44px
						&.highLight
							color: #fff
				.num
					position: absolute
					top: 0
					right: 0
					width: 24px
					height: 16px
					line-height: 16px
					border-radius: 12px
					background: #f01414	
					box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)	
					color: #fff
					font-size: 9px
					font-weight: 700
					text-align: center
			.price,.desc
				display: inline-block
				vertical-align: top
				font-size: 10px
				font-weight: 700
				line-height: 24px
				color: rgba(255,255,255,0.4)
				margin-top: 12px
				&.highLight
					color: #fff
			.price	
				padding-right: 12px
				box-sizing: border-box
				border-right: solid 1px rgba(255,255,255,0.1)
			.desc
				margin-left: 12px
		.content-right
			flex: 0 0 105px
			width: 105px
			padding: 0 8px
			background: rgba(255,255,255,0.4)
			&.enough
				background: #00b43c
				.text
					color: #fff
			.text
				margin-top: 12px
				font-size: 12px
				color: rgba(255,255,255,0.4)
				font-weight: 700
				line-height: 24px
				text-align: center
	.ball-container
		.ball
			position: fixed
			left: 32px
			bottom: 22px
			z-index:  50
			transition: all 0.5s cubic-bezier(0.49, -0.29, 0.75, 0.41)
			.inner
				width: 16px
				height: 16px
				border-radius: 50%
				background: rgb(0, 160, 220)
				transition: all 0.5s linear
	.shopcart-list
		position: absolute
		top: 0
		left: 0
		z-index: -1
		width: 100%
		transform: translate3d(0, -100%, 0)
		&.fold-enter-active, &.fold-leave-active
			transition: all 0.5s
		&.fold-enter, &.fold-leave-to
			transform: translate3d(0,0,0)
		.list-header
			height: 40px
			line-height: 40px
			padding: 0 18px
			background: #f3f5f7
			border-bottom: 2px solid rgba(7,17,27,0.1)
			.title
				font-size: 14px
				color: rgb(7,17,27)
				float: left
			.empty
				font-size: 12px
				color: rgb(0,160,220)
				float: right
		.list-content
			padding: 0 18px
			max-height: 217px
			overflow: hidden
			background: #fff
			.food
				position: relative
				padding: 12px 0
				box-sizing: border-box
				border-1px(rgba(7, 17, 27, 0.1))
				.name
					line-height: 24px
					font-size: 14px
				.price
					position: absolute
					right: 90px
					bottom: 12px
					line-height: 24px
					font-size: 14px
					font-weight: 700
					color: rgb(240, 20, 20)
				.cartcontrol-wrapper
					position: absolute
					right: 0
					bottom: 6px
.list-mask
	position: fixed
	top: 0
	left: 0
	width: 100%
	height: 100%
	z-index: 10
	backdrop-filter: blur(10px)
	opacity: 1
	background: rgba(7, 17, 27, 0.6)
	&.fade-enter-active, &.fade-leave-active
		transition: all 0.5s
	&.fade-enter, &.fade-leave-active
		opacity: 0
		background: rgba(7, 17, 27, 0)
</style>