<template>
  <div class="goods">
    <div class="menu-wrapper" id="menu-scroll">
      <ul class="menu">
        <li v-for="(category,$index) in goods" class="menu-item" :class="{'current': $index === currentIndex}"
            @click="selectMenu($event,$index)">
            <span class="text border-1px">
              <support-icon v-if="category.type >= 0" :supportType="category.type" class="icon" type="3">
              </support-icon>{{category.name}}
            </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" id="food-scroll">
      <ul class="categories">
        <li v-for="category in goods" class="food-list-hook">
          <h2 class="category">{{category.name}}</h2>
          <ul class="foods">
            <li v-for="food in category.foods" @click="selectFood(food, $event)" class="food border-1px">
              <div class="avatar">
                <img width="72px" height="72px" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p v-if="food.description" class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="sell-count">月售{{food.sellCount}}份</span><span
                  class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span
                  v-if="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
                </div>
                <cartcontrol :food="food" :drop="true" class="cartcontrol"></cartcontrol>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <food :food="selectedFood" ref="foodDetail"></food>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from "vue"
  import Vuex, {mapState, mapGetters, mapMutations} from "vuex"
  import support from "../components/support/support-icon.vue"
  import cartcontrol from "../cartcontrol/cartcontrol.vue"
  import food from '../food/food.vue'
  import BScroll from "better-scroll"

  const ERR_OK = 0



  export default {
    data() {
      return {
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    created() {
      this.$http.get('/api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.initGoods({goods: response.data});
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          })
        }
      })
    },
    computed: {
      currentIndex(){
        for (let i = 0; i < this.listHeight.length - 1; i++) {
          if (this.scrollY >= this.listHeight[i] && this.scrollY < this.listHeight[i + 1]) {
            return i;
          }
        }
        return 0;
      },
      ...mapState(['goods']),
      ...mapGetters(['selectedFoods'])
    },
    methods: {
      ...mapMutations(['initGoods']),
      _initScroll(){
        this.menuScroll = new BScroll(document.getElementById('menu-scroll'), {
          click: true
        });
        this.foodScroll = new BScroll(document.getElementById('food-scroll'), {
          click: true,
          probeType: 3 //实时发送滚动位置
        })
        this.foodScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        })
      },
      _calculateHeight(){
        let list = document.getElementById('food-scroll').getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < list.length; i++) {
          height += list[i].clientHeight
          this.listHeight.push(height);
        }
      },
      selectMenu(event, index){
        //防止PC下触发两次click，_constructed为BScroll触发的click事件独有的
        if (!event._constructed) {
          return;
        }
        this.foodScroll.scrollTo(0, -(this.listHeight[index]), 0.5);
      },
      _drop(el){
        this.$refs.shopcart.drop(el);
      },
      selectFood(food, event){
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.foodDetail.toggleShow({_constructed: true});
      }
    },
    components: {
      "support-icon": support,
      cartcontrol,
      food
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    flex-direction: row
    position: absolute
    top: 174px
    bottom: 48px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: none
      width: 80px
      background-color: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        padding: 0 12px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background-color: #fff
          font-weight: 700
          .text
            border-none()
        .text
          display: table-cell
          vertical-align: middle
          width: 56px
          font-size: 12px
          border-1px(rgba(7, 17, 27, 0.1))
    .foods-wrapper
      flex: auto
      .category
        padding-left: 14px
        height: 26px
        line-height: 26px
        font-size: 12px
        color: rgb(147, 153, 159)
        background-color: #f3f5f7
        border-left: 1px solid #d9dde1
      .food
        display: flex
        margin: 18px 18px 0 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-bottom: 0
        .avatar
          flex: 0 0 72px
          margin-right: 10px
        .content
          flex auto
          padding-top: 2px
          .name
            display: block
            margin-bottom: 8px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc, .extra
            margin-bottom: 8px
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
            .rating
              margin-left: 12px
          .price
            line-height: 24px
            font-weight: 700
            .now
              font-size: 14px
              color: rgb(140, 20, 20)
            .old
              margin-left: 8px
              font-size: 10px
              text-decoration: line-through
              color: rgb(147, 153, 159)
          .cartcontrol
            position: absolute
            right: 0px
            bottom: 10px


</style>
