<template>
  <div v-show="showFlag" class="food" transition="move" v-el:food>
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="now">￥{{food.price}}</span>
          <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0" transition="fade">加入购物车
        </div>
      </div>
      <split v-show="food.info"></split>
      <div class="info" v-show="food.info">
        <h1 class="title">商品信息</h1>
        <p class="text">{{food.info}}</p>
      </div>
      <split v-show="food.info"></split>
      <div class="rating">
        <h2 class="title">商品评价</h2>
        <ratingselect :only-content="onlyContent" :select-type="selectType" :desc="desc" :ratings="food.ratings"></ratingselect>
        <div class="rating-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings"
                class="rating-item border-1px">
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img class="avatar" :src="rating.avatar">
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
              <p class="text">
                <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
              </p>
            </li>
          </ul>
          <!--没有评价显示-->
          <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import BScroll from 'better-scroll'
  import Vue from 'vue'
  import split from 'components/split/split'
  import ratingselect from 'components/ratingselect/ratingselect'
  const ALL = 2
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      show () {
        this.showFlag = true
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$els.food, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      hide () {
        this.showFlag = false
      },
      addFirst (event) {
        if (!event._constructed) {
          return
        }
        this.$dispatch('cart.add', event.target)
        Vue.set(this.food, 'count', 1)
      },
      needShow (type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    },
    events: {
      'ratingtype.select' (type) {
        this.selectType = type
      },
      'content.toggle' (onlyContent) {
        this.onlyContent = onlyContent
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .food
    position fixed
    left 0
    top 0
    bottom 2.4rem
    z-index 30
    width 100%
    background #fff
    &.move-transition
      transition all 0.2s linear
      transform translate3d(0, 0, 0)
    &.move-enter, &.move-leave
      transform translate3d(100%, 0, 0)
    .image-header
      position relative
      width 100%
      height 0
      padding-top 100%
      img
        position absolute
        top 0
        left 0
        width 100%
        height 100%
      .back
        position absolute
        top .5rem
        left 0
        .icon-arrow_lift
          display block
          padding .5rem
          font-size 1rem
          color #fff
    .content
      position relative
      padding .9rem
      .title
        line-height .7rem
        margin-bottom .4rem
        font-size .7rem
        font-weight 700
        color rgb(7, 17, 27)
      .detail
        margin-bottom .9rem
        line-height .5rem
        height .5rem
        font-size 0
        .sell-count, .rating
          font-size .5rem
          color rgb(147, 153, 159)
        .sell-count
          margin-right .6rem
      .price
        font-weight 700
        line-height 1.2rem
        font-size 0
        .now
          margin-right .4rem
          font-size .7rem
          color rgb(240, 20, 20)
        .old
          text-decoration line-through
          font-size .5rem
          color rgb(147, 153, 159)
      .cartcontrol-wrapper
        position absolute
        right .6rem
        bottom .8rem
      .buy
        position absolute
        right .9rem
        bottom 1.1rem
        z-index 10
        height 1.2rem
        line-height 1.2rem
        padding 0 .6rem
        box-sizing border-box
        border-radius .6rem
        font-size .5rem
        color #fff
        background rgb(0, 160, 220)
        &.fade-transition
          transition all 0.2s
          opacity 1
        &.fade-enter, &.fade-leave
          opacity 0
    .info
      padding .9rem
      .title
        line-height .7rem
        margin-bottom .3rem
        font-size .7rem
        color rgb(7, 17, 27)
      .text
        line-height 1.2rem
        font-size .6rem
        color rgb(77, 85, 93)
    .rating
      padding-top .9rem
      .title
        line-height .7rem
        margin-left .9rem
        font-size .7rem
        color rgb(7, 17, 27)
      .rating-wrapper
        padding: 0 .9rem
        .rating-item
          position relative
          padding .8rem 0
          border-1px(rgba(7, 17, 27, 0.1))
          .user
            position absolute
            right 0
            top .8rem
            line-height .6rem
            font-size 0
            .name
              display inline-block
              margin-right .3rem
              vertical-align top
              font-size .5rem
              color rgb(147, 153, 159)
            .avatar
              width .6rem
              height .6rem
              border-radius 50%
          .time
            margin-bottom .3rem
            line-height .6rem
            font-size .5rem
            color rgb(147, 153, 159)
          .text
            line-height .8rem
            font-size .6rem
            color rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              margin-right .2rem
              line-height .8rem
              font-size .6rem
            .icon-thumb_up
              color: rgb(0, 160, 220)
            .icon-thumb_down
              color: rgb(147, 153, 159)

        .no-rating
          padding .8rem 0
          font-size .6rem
          color rgb(147, 153, 159)
</style>
