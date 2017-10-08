<template>
	<div class="cartcontrol">
		<transition name="move">
			<div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCount">
				<span class="icon-remove_circle_outline"></span>
			</div>
		</transition>
		<div class="cart-count" v-show="food.count>0">
			{{food.count}}
		</div>
		<div class="cart-add">
			<span class="icon-add_circle" @click.stop.prevent="addCount"></span>
		</div>
	</div>
</template>

<script>
	import Vue from 'vue'
	export default {
		props: {
			food: {
				type: Object
			}
		},
		methods: {
			addCount (event) {
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
			decreaseCount (event) {
				if (!event._constructed) {
		        	return;
		        }
				if (this.food.count > 0) {
					this.food.count--;
				}
			}
		}
	}
</script>

<style lang="stylus" rel="stylesheet/stylus">
.cartcontrol
	font-size: 0
	.cart-decrease
		display: inline-block
		vertical-align: top
		line-height: 24px
		font-size: 24px
		color: rgb(0,160,220)
		&.move-enter-active, &.move-leave-active
			transition: all .5s linear	
		&.move-enter, &.move-leave-to
			opacity: 0
			transform: translateX(24px) rotate(180deg)	
	.cart-count
		display: inline-block
		vertical-align: top
		width: 24px
		font-size: 10px
		color: rgb(147,153,159)
		line-height: 24px
		text-align: center
	.cart-add
		display: inline-block
		vertical-align: top
		font-size: 24px
		color: rgb(0,160,220)
		line-height: 24px
		
</style>