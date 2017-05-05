<template>
  <div class="goods">
    <!--左侧栏-->
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{'current':currentIndex===$index}"
            @click="selectMenu($index,$event)">
          <div class="text border-1px">
            <span v-if="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </div>
        </li>
      </ul>
    </div>
    <!--右侧栏-->
    <div class="foods-wrapper" v-el:foods-wrapper>
      <ul>
        <li v-for="item in goods" class="food-list-hook">
          <!--标题-->
          <h1 class="title">{{ item.name }}</h1>
          <!--内容-->
          <ul>
            <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">¥{{food.price}}</span><span class="old"
                                                                v-show="food.oldPrice">¥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!--购物车-->
    <shopcart v-ref:shopcart :select-foods="selectFoods"
              :delivery-price="seller.deliveryPrice"
              :min-price="seller.minPrice"></shopcart>
    <!--food详情-->
    <food :food="selectedFood" v-ref:food></food>
  </div>
</template>
<script type="text/ecmascript-6">
  /**
   *  this.$nextTick 将回调延迟到下次 DOM 更新循环之后执行。体验优化
   *  在修改数据之后立即使用它，然后等待 DOM 更新
   *  events 接受派发事件
   * v-ref 在父组件上注册一个子组件的索引，便于直接访问。不需要表达式。必须提供参数 id。
   * 可以通过父组件的 $refs 对象访问子组件。
   */
  import BScroll from 'better-scroll'
  import shopcart from 'components/shopcart/shopcart'
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import food from 'components/food/food'

  const ERR_OK = 0
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods () {
        let foods = []
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
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
    },
    methods: {
      _initScroll () {
        this.meunScroll = new BScroll(this.$els.menuWrapper, {
          click: true
        })

        this.foodsScroll = new BScroll(this.$els.foodsWrapper, {
          click: true,
          probeType: 3
        })

        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight () {
        let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (var i = 0; i < foodList.length; i++) {
          let item = foodList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      selectMenu (index, event) {
        if (!event._constructed) {
          return
        }
        let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index]
        this.foodsScroll.scrollToElement(el, 300)
      },
      _drop (target) {
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target)
        })
      },
      selectFood (food, event) {
        if (!event._constructed) {
          return
        }
        this.selectedFood = food
        this.$refs.food.show()
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    },
    events: {
      'cart.add' (target) {
        this._drop(target)
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .goods
    position absolute
    top 8.7rem
    bottom 2.4rem
    width 100%
    display flex
    overflow hidden
    .menu-wrapper
      width 4rem
      flex 0 0 4rem
      background-color #f3f5f7
      .menu-item
        display: table
        height: 2.7rem
        padding: 0 .6rem
        line-height: .7rem
        &.current
          position relative
          z-index 10
          margin-top -.05rem
          background #fff
          font-weight 700
          .text
            border-none()
        .icon
          display: inline-block
          vertical-align: top
          width: .6rem
          height: .6rem
          margin-right: .1rem
          background-size: .6rem .6rem
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
        .text
          display table-cell
          width 2.8rem
          vertical-align middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size .6rem
    .foods-wrapper
      flex 1
      .title
        padding-left .7rem
        height 1.3rem
        line-height 1.3rem
        border-left 2px solid #d9dde1
        font-size .6rem
        color rgb(147, 153, 159)
        background-color #f3f5f7
      .food-item
        display flex
        margin .9rem
        padding-bottom .9rem
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom 0
        .icon
          width 2.8rem
          flex 0 0 2.8rem
          margin-right .5rem
          img
            width 2.8rem
            height 2.8rem
        .content
          flex 1
          .name
            margin .1rem 0 .4rem 0
            height .7rem
            line-height .7rem
            font-size .7rem
            color rgb(7, 17, 27)
          .desc, .extra
            line-height .5rem
            font-size .5rem
            color rgb(147, 153, 159)
          .desc
            line-height .8rem
            margin-bottom .4rem
          .extra
            .count
              margin-right .5rem
          .price
            font-weight: 700
            line-height: 1.2rem
            .now
              margin-right: .4rem
              font-size: .7rem
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: .5rem
              color: rgb(147, 153, 159)
          .cartcontrol-wrapper
            position absolute
            right 0
            bottom .6rem


</style>

