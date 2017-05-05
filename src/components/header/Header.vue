<template>
  <div class="header">
    <!--商家-->
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <!--公告-->
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <!--背景-->
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <!--公告详情-->
    <div v-show="detailShow" class="detail" transition="fade">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <!--星星组件 传入size尺寸 score 分数-->
          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li class="support-item" v-for="item in seller.supports">
              <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
              <span class="text">{{seller.supports[$index].description}}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <!--关闭icon-->
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star'
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        detailShow: false,
        classMap: []
      }
    },
    methods: {
      showDetail () {
        this.detailShow = true
      },
      hideDetail () {
        this.detailShow = false
      }
    },
    created () {
      /**
       *  Vue.js 的过渡系统  transition="fade"   .fade-transition .fade-enter .fade-leave
       * 配置优惠 classMap 数组
       */
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    components: {
      star: star
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .header
    position relative
    overflow hidden
    color #fff
    background rgba(7, 17, 27, 0.5)
    .content-wrapper
      position relative
      padding 1.2rem .6rem .9rem 1.2rem
      font-size 0
      .avatar
        display inline-block
        vertical-align top
        img
          border-radius 2px
          width 3.2rem
      .content
        display inline-block
        margin-left .8rem
        .title
          margin .1rem 0 .4rem 0
          .brand
            display inline-block
            width 1.5rem
            height .9rem
            bg-image('brand')
            background-size 1.5rem .9rem
            background-repeat no-repeat
            vertical-align top
          .name
            margin-left .3rem
            font-size .8rem
            font-weight bold
            line-height .9rem
        .description
          margin-bottom .5rem
          line-height .6rem
          font-size .6rem
        .support
          .icon
            display inline-block
            vertical-align top
            width .6rem
            height .6rem
            margin-right .2rem
            background-size .6rem .6rem
            background-repeat no-repeat
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
            line-height .6rem
            font-size .5rem

      .support-count
        position absolute
        right .6rem
        bottom .7rem
        padding 0 .4rem
        height 1.2rem
        line-height 1.2rem
        border-radius .7rem
        background rgba(0, 0, 0, 0.2)
        text-align center
        .count
          vertical-align top
          font-size .5rem
        .icon-keyboard_arrow_right
          line-height 1.2rem
          font-size .5rem

    .bulletin-wrapper
      position relative
      height 1.4rem
      line-height 1.4rem
      padding 0 1.1rem 0 1.1rem
      white-space nowrap
      overflow hidden
      text-overflow ellipsis
      background rgba(7, 17, 27, 0.2)
      .bulletin-title
        display inline-block
        vertical-align top
        margin-top .4rem
        width 1.2rem
        height .6rem
        bg-image('bulletin')
        background-size: 1.2rem .6rem
        background-repeat: no-repeat
      .bulletin-text
        vertical-align top
        margin 0 .2rem
        font-size .5rem
      .icon-keyboard_arrow_right
        position absolute
        font-size .5rem
        right .6rem
        top .4rem

    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
    .detail
      position fixed
      z-index 100
      top 0
      left 0
      width 100%
      height 100%
      overflow auto
      transition all 0.5s
      backdrop-filter blur(10px)
      &.fade-transition   //过度
        opacity 1
        background rgba(7, 17, 27, 0.8)
      &.fade-enter, &.fade-leave  // 进入或离开的过度
        opacity 0
        background rgba(7, 17, 27, 0)
      .detail-wrapper
        width 100%
        min-height 100%
        .detail-main
          margin-top 3.2rem
          padding-bottom 3.2rem
          .name
            line-height .8rem
            text-align: center
            font-size .8rem
            font-weight: 700
          .star-wrapper
            margin-top .9rem
            padding .1rem 0
            text-align: center
          .title
            display flex
            width 80%
            margin 1.4rem auto 1.4rem auto
            .line
              flex 1
              position relative
              top -.3rem
              border-bottom 1px solid rgba(255, 255, 255, 0.2)
            .text
              padding 0 .6rem
              font-weight 700
              font-size .7rem

          .supports
            width 80%
            margin 0 auto
            .support-item
              padding 0 .6rem
              margin-bottom .6rem
              font-size 0
              &:last-child
                margin-bottom 0
              .icon
                display inline-block
                width .8rem
                height .8rem
                vertical-align top
                margin-right .3rem
                background-size .8rem .8rem
                background-repeat no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
              .text
                line-height .8rem
                font-size .6rem
          .bulletin
            width: 80%
            margin: 0 auto
            .content
              padding: 0 .6rem
              line-height: 1.2rem
              font-size: .6rem
      .detail-close
        position relative
        width 1.6rem
        height 1.6rem
        margin -64px auto 0 auto
        clear both
        font-size 1.6rem
</style>
