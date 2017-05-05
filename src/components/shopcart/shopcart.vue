<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left" @click="toggleList">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight':totalCount>0}">
            <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
          </div>
          <div class="num" v-if="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight':totalPrice>0}">¥&nbsp;{{totalPrice}}</div>
        <div class="desc">另需配送费¥&nbsp;{{deliveryPrice}}</div>
      </div>
      <div class="content-right" @click.stop.prevent="pay">
        <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
      </div>
    </div>
    <div class="ball-container">
      <div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
        <div class="inner inner-hook"></div>
      </div>
    </div>
    <div class="shopcart-list" v-show="listShow" transition="fold">
      <div class="list-header border-1px">
        <h1 class="title">购物车</h1>
        <span class="empty" @click.stop="empty">清空</span>
      </div>
      <div class="list-content" v-el:list-content>
        <ul>
          <li class="food border-1px" v-for="food in selectFoods">
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
  </div>
  <div class="list-mask" @click="hideList" v-show="listShow" transition="fade"></div>
</template>
<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import BScroll from 'better-scroll'
  export default {
    props: {
      selectFoods: {
        type: Array,
        default () {
          return [
            {
              price: 10,
              count: 1
            }
          ]
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data () {
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
      totalPrice () {
        let total = 0
        this.selectFoods.forEach((food) => {
          total += food.price * food.count
        })
        return total
      },
      totalCount () {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      },
      payDesc () {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice
          return `还差￥${diff}元起送`
        } else {
          return '去结算'
        }
      },
      payClass () {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough'
        } else {
          return 'enough'
        }
      },
      listShow () {
        if (!this.totalCount) {
          this.fold = true
        }
        let show = !this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$els.listContent, {
                click: true
              })
            } else {
              this.scroll.refresh()
            }
          })
        }
        return show
      }
    },
    methods: {
      pay () {
        if (this.totalPrice < this.minPrice) {
          return
        }
        window.alert(`支付${this.totalPrice}元`)
      },
      drop (el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i]
          if (!ball.show) {
            ball.show = true
            ball.el = el
            this.dropBalls.push(ball)
            return
          }
        }
      },
      toggleList () {
        if (!this.totalCount) {
          return
        }
        this.fold = !this.fold
      },
      hideList () {
        this.fold = true
      },
      empty () {
        this.selectFoods.forEach((food) => {
          food.count = 0
        })
      }
    },
    transitions: {
      drop: {
        beforeEnter (el) {
          let count = this.balls.length
          while (count--) {
            let ball = this.balls[count]
            if (ball.show) {
              let rect = ball.el.getBoundingClientRect()
              let x = rect.left - 32
              let y = -(window.innerHeight - rect.top - 22)
              el.style.display = ''
              el.style.webkitTransform = `translate3d(0,${y}px,0)`
              el.style.transform = `translate3d(0,${y}px,0)`
              let inner = el.getElementsByClassName('inner-hook')[0]
              inner.style.webkitTransform = `translate3d(${x}px,0,0)`
              inner.style.transform = `translate3d(${x}px,0,0)`
            }
          }
        },
        enter (el) {
          /* eslint-disable no-unused-vars */
          let rf = el.offsetHeight
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)'
            el.style.transform = 'translate3d(0,0,0)'
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.webkitTransform = 'translate3d(0,0,0)'
            inner.style.transform = 'translate3d(0,0,0)'
          })
        },
        afterEnter (el) {
          let ball = this.dropBalls.shift()
          if (ball) {
            ball.show = false
            el.style.display = 'none'
          }
        }
      }
    },
    components: {
      cartcontrol
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .shopcart
    position fixed
    bottom 0
    height 2.4rem
    width 100%
    z-index 50
    left 0
    .content
      display flex
      background-color #141d27
      font-size 0
      color rgba(255, 255, 255, 0.4)
      .content-left
        flex 1
        .logo-wrapper
          display inline-block
          vertical-align top
          position relative
          top -.5rem
          margin 0 0.4rem
          padding .3rem
          width 2.8rem
          height 2.8rem
          box-sizing border-box
          border-radius 50%
          background-color #141d27
          .logo
            width 100%
            height 100%
            border-radius 50%
            text-align center
            background #2b343c
            &.highlight
              background-color rgb(0, 160, 220)
            .icon-shopping_cart
              line-height 2.2rem
              font-size 1.2rem
              color #80858a
              &.highlight
                color #fff
        .price
          display inline-block
          vertical-align top
          margin-top .6rem
          line-height 1.2rem
          padding-right .6rem
          box-sizing border-box
          border-right 1px solid rgba(255, 255, 255, 0.1)
          font-size .8rem
          font-weight 700
          &.highlight
            color: #fff
        .desc
          display inline-block
          vertical-align top
          margin .6rem 0 0 .1rem
          line-height 1.2rem
          font-size .5rem
        .num
          position absolute
          top 0
          right 0
          width 1.2rem
          height .8rem
          line-height .8rem
          text-align: center
          border-radius .8rem
          font-size .5rem
          font-weight 700
          color #fff
          background-color rgb(240, 20, 20)
          box-shadow 0 .2rem .4rem 0 rgba(0, 0, 0, 0.4)
      .content-right
        flex 0 0 5.3rem
        width 5.3rem
        .pay
          height 2.4rem
          line-height 2.4rem
          text-align center
          font-size .6rem
          font-weight 700
          &.not-enough
            background #2b333b
          &.enough
            background #00b43c
            color #fff
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        &.drop-transition
          transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
          .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background: rgb(0, 160, 220)
            transition: all 0.4s linear
    .shopcart-list
      position absolute
      left 0
      top 0
      z-index -1
      width 100%
      &.fold-transition
        transition all 0.5s
        transform translate3d(0, -100%, 0)
      &.fold-enter, &.fold-leave
        transform translate3d(0, 0, 0)
      .list-header
        height 2rem
        line-height: 2rem
        padding: 0 .9rem
        background: #f3f5f7
        border-1px(rgba(7, 17, 27, 0.1))
        clear both
        .title
          float left
          font-size .7rem
          color rgb(7, 17, 27)
        .empty
          float right
          font-size .7rem
          color rgb(0, 160, 220)
      .list-content
        padding 0 .9rem
        max-height 11rem
        overflow hidden
        background #fff
        .food
          position relative
          padding .6rem 0
          box-sizing border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height 1.2rem
            font-size .7rem
            color rgb(7, 17, 27)
          .price
            position absolute
            right 4.5rem
            bottom .6rem
            line-height 1.2rem
            font-size .4rem
            font-weight 700
            color rgb(240, 20, 20)
          .cartcontrol-wrapper
            position absolute
            right 0
            bottom .3rem

  .list-mask
    position fixed
    top 0
    left 0
    width 100%
    height 100%
    z-index 40
    backdrop-filter blur(10px)
    &.fade-transition
      transition all 0.5s
      opacity 1
      background rgba(7, 17, 27, 0.6)
    &.fade-enter, &.fade-leave
      opacity 0
      background rgba(7, 17, 27, 0)
</style>
