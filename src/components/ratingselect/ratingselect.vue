<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2,$event)" class="block positive" :class="{'active':selectType===2}">{{ desc.all }}<i
        class="count">{{ratings.length}}</i></span>
      <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{ desc.positive }}<i
        class="count">{{positives.length}}</i></span>
      <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{ desc.negative }}<i
        class="count">{{negatives.length}}</i></span>
    </div>
    <div @click="toggleContent" class="switch border-1px" :class="{'on':onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  /**
   * 子组件 和父组件通信
   * 子组件通过派发事件 $dispatch
   *
   */
  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2
  export default {
    props: {
      ratings: {
        type: Array,
        default () {
          return []
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default () {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          }
        }
      }
    },
    computed: {
      positives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE
        })
      },
      negatives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE
        })
      }
    },
    methods: {
      select (type, event) {
        if (!event._constructed) {
          return
        }
        this.selectType = type
        this.$dispatch('ratingtype.select', type)
      },
      toggleContent (event) {
        if (!event._constructed) {
          return
        }
        this.onlyContent = !this.onlyContent
        this.$dispatch('content.toggle', this.onlyContent)
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratingselect
    .rating-type
      padding .9rem 0
      margin 0 .9rem
      border-1px(rgba(7, 17, 27, 0.1))
      font-size 0
      .block
        display inline-block
        padding .4rem .6rem
        margin-right .4rem
        line-height .8rem
        border-radius 1px
        font-size .6rem
        color rgb(77, 85, 93)
        &.active
          color #fff
        .count
          margin-left .1rem
          font-size .4rem
          font-style normal
        &.positive
          background rgba(0, 160, 220, 0.2)
          &.active
            background rgb(0, 160, 220)
        &.negative
          background rgba(77, 85, 93, 0.2)
          &.active
            background rgb(77, 85, 93)
    .switch
      padding .6rem .9rem
      line-height 1.2rem
      border-1px(rgba(7, 17, 27, 0.1))
      color rgb(147, 153, 159)
      font-size 0
      &.on
        .icon-check_circle
          color #00c850
      .icon-check_circle
        display inline-block
        vertical-align top
        margin-right .2rem
        font-size 1.2rem
      .text
        display inline-block
        vertical-align top
        font-size .6rem
</style>
