<template>
  <div>
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <a v-link="{path:'/goods'}">商品</a>
      </div>
      <div class="tab-item">
        <a v-link="{path:'/ratings'}">评论</a>
      </div>
      <div class="tab-item">
        <a v-link="{path:'/seller'}">商家</a>
      </div>
    </div>
    <!-- route outlet -->
    <router-view :seller="seller" ></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
  /**
   * vue的生命周期 created beforeCompile Compiled ready beforeDestroy Destroy
   * Object.assign()Object.assign() 方法用于将所有可枚举的属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。
   */
  import header from 'components/header/header'
  const ERR_OK = 0
  export default {
    data () {
      return {
        seller: {}
      }
    },
    created () {
      this.$http.get('/api/seller').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.seller = Object.assign({}, this.seller, response.data)
        }
      }, (response) => {
        console.log(response)
      })
    },
    components: {
      'v-header': header
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "common/stylus/mixin.styl"

  #app
    .tab
      width 100%
      height 2rem
      line-height 2rem
      display flex
      border-1px(rgba(7, 17, 27, 0.1))
      position relative
      .tab-item
        flex 1
        text-align center
        & > a
          display block
          font-size .7rem
          color rgb(77, 85, 93)
          &.active
            color rgb(240, 20, 20)
</style>
