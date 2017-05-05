<template>
  <div class="cartcontrol">
    <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart"
         transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  /**
   * food.count 属性不存在
   * 使用Vue.set( element,attr,value) 为某个对象添加属性
   * $dispatch 广播事件 this.$dispatch(event,[args]) 派发事件
   * 首先在实例上触发它，然后沿着父链向上冒泡在触发一个监听器后停止
   * 除非它返回 true。附加参数都会传给监听器回调。
   *
   */
  import Vue from 'vue'
  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart (event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        this.$dispatch('cart.add', event.target)
      },
      decreaseCart (event) {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size 0
    .cart-decrease
      display inline-block
      padding .3rem
      transition all 0.4s linear
      &.move-transition
        opacity 1
        transform translate3d(0, 0, 0)
        .inner
          display inline-block
          line-height 1.2rem
          font-size 1.2rem
          color rgb(0, 160, 220)
          transition all 0.4s linear
          transform rotate(0)
      &.move-enter, &.move-leave
        opacity 0
        transform translate3d(1.2rem, 0, 0)
        .inner
          transform rotate(180deg)
    .cart-count
      display inline-block
      vertical-align top
      width .6rem
      padding-top .3rem
      line-height 1.2rem
      text-align center
      font-size .5rem
      color rgb(147, 153, 159)
    .cart-add
      display inline-block
      padding .3rem
      line-height 1.2rem
      font-size 1.2rem
      color rgb(0, 160, 220)
</style>
