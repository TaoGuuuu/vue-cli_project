<template>
	<div class="goods">
		<div class="menu-wrapper" ref='menuWrapper'>
		<ul>
			<li v-for="(item, index) in goods" class="menu-item border-1px" :class="{'current':currentIndex === index}" @click="selectMenu(index, $event)"  ref="menuList">
				<span class="text"><span class="icon" v-show="item.type > 0" :class="classMap[item.type]"></span>{{item.name}}</span>
			</li>
		</ul>
		</div>
		<div class="foods-wrapper" ref='foodsWrapper'>
			<ul>
				<li v-for="item in goods" class="foods-item" ref='foodList'>
					<h2 class="foods-title">{{item.name}}</h2>
					<div class="foods-content">
						<ul>
							<li @click="selectFood(food,$event)" v-for="food in item.foods" class="foods border-1px">
								<img class="foods-icon" v-bind:src="food.icon">
								<div class="foods-text">
									<h3 class="food-name">{{food.name}}</h3>
									<p class="food-description">{{food.description}}</p>
									<div class="food-description">
										<span>月售{{food.sellCount}}份</span><span class="food-rating">好评率{{food.rating}}%</span>
									</div>
									<div class="price">
										<span class="food-price">￥{{food.price}}</span><span class="food-oldPrice" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
									</div>
									<div class="cartcontrol-wrapper">
										<cartcontrol :food="food" @add="addFood"></cartcontrol>
									</div>
								</div>
							</li>
						</ul>
					</div>
				</li>
			</ul>
		</div>
		<shopcart ref="shopcart" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" :select-foods="selectFoods"></shopcart>
		<food :food="selectedFood" ref="food" @add="addFood"></food>
	</div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart.vue'
import cartcontrol from '../cartcontrol/cartcontrol.vue'
import food from '../food/food.vue'
const ERR_OK = 0
export default {
	props: {
		seller:{
			type: Object
		}
	},
	data() {
		return {
			goods:[],
			listHeight:[],
			scrollY: 0,
			selectedFood: {}
		}
	},
	computed: {
		currentIndex() {
			for (let i = 0; i < this.listHeight.length; i++) {
				let height1 = this.listHeight[i];
				let height2 = this.listHeight[i + 1];
				if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
					this._followScroll(i);
					return i;
				}
			}
			return 0;
		},
		selectFoods() {
			let foods = [];
			this.goods.forEach((good) => {
				good.foods.forEach((food) => {
					if (food.count) {
						foods.push(food)
					}
				})
			})
			return foods
		}
	},
	created() {
		this.$http.get('/api/goods').then(response => {
		    response = response.body;
		    if (response.errno === ERR_OK){
		    	this.goods = response.data;
		    	this.$nextTick(() => {
		    		this._initScroll();
		    		this._calculateHeight()
		    	})
		    }
		});
		this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
	},
	methods: {
		_initScroll() {
			this.menuScroll = new BScroll(this.$refs.menuWrapper, {
				click: true
			});
			this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
				click: true,
				probeType: 3
			});
			this.foodsScroll.on('scroll', (pos) => {
				// 判断滑动方向，避免下拉时分类高亮错误（如第一分类商品数量为1时，下拉使得第二分类高亮）
		        if (pos.y <= 0) {
		            this.scrollY = Math.abs(Math.round(pos.y)) + 1;
		        }
			})
		},
		_calculateHeight() {
			let foodList = this.$refs.foodList;
			let height = 0;
			this.listHeight.push(height);
			for (let i = 0; i < foodList.length; i++) {
				let item = foodList[i];
				height += item.clientHeight;
				this.listHeight.push(height)
			}
		},
	    _followScroll(index) {
	        let menuList = this.$refs.menuList;
	        let el = menuList[index];
	        this.menuScroll.scrollToElement(el, 300, 0, -100);
	    },
		selectMenu(index, event) {
			if (!event._constructed) {
				return
			};
			let foodList = this.$refs.foodList;
			let el = foodList[index];
			this.foodsScroll.scrollToElement(el, 300)
		},
		addFood(target) {
	    	this._drop(target);
	   	},
	    _drop(target) {
        	// 体验优化,异步执行下落动画
        	this.$nextTick(() => {
          		this.$refs.shopcart.drop(target);
        	});
        },
        selectFood(food, event) {
        	if (!event._constructed) {
        		return
        	}
        	this.selectedFood = food;
        	this.$refs.food.show()
        }
	},
	components: {
		shopcart,
		cartcontrol,
		food
	}
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin"
.goods
	display: flex
	position: absolute
	top: 174px
	bottom: 46px
	width: 100%
	overflow: hidden
	.menu-wrapper
		flex: 0 0 80px
		width: 80px
		background: #f3f5f7
		.menu-item	
			display: table
			height: 54px
			width: 56px
			padding: 0 12px
			line-height: 14px
			border-1px(rgba(7,17,27,0.1))
			&.current
				position: relative
				z-index: 10
				margin-top: -1px
				background: #fff
				border-none()
				.text
					font-weight: 700
			.text
				display: table-cell
				font-size: 12px
				vertical-align: middle	
				.icon
					display: inline-block
					vertical-align: top
					height: 12px
					width: 12px
					background-size: 12px 12px
					background-repeat: no-repeat
					&.decrease
						bg-image('decrease_3')
					&.discount
						bg-image('discount_3')
					&.guarantee
						bg-image('guarantee_3')
					&.invoice
						bg-image('invoice_3')
					&.special
						bg-image('special_3')			
	.foods-wrapper
		flex: 1
		.foods-item
			.foods-title
				height: 26px
				font-size: 12px
				color: rgb(147,153,159)
				line-height: 26px
				background: #f3f5f7
				padding-left: 14px
				border-left: 2px solid #d9dde1
			.foods-content
				.foods
					display: flex
					margin: 18px 18px 0 18px
					padding-bottom: 18px
					border-1px(rgba(7,17,27,0.1))
					&:last-child
						border-none()
					.foods-icon
						flex: 0 0 57px
						width: 57px
						height: 57px
					.foods-text
						flex: 1
						display: inline-block
						vertical-align: top
						margin: 2px 0 0 10px 
						.food-name
							font-size: 14px
							color: rgb(7,17,27)
							line-height: 14px
						.food-description
							font-size: 10px
							line-height: 14px
							color: rgb(147,153,159)
							margin-top: 8px
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
						.cartcontrol-wrapper
							position: absolute
							right: 0
							bottom: 18px
</style>