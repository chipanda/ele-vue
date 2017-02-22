<template>
  <transition name="foodDetail">
    <div class="foodDetail" id="foodDetail" v-show="show">
      <div>
        <div class="img-header">
          <img :src="food.image">
          <i class="icon-arrow_lift back-hook" @click.stop.prevent="toggleShow"></i>
        </div>
        <div class="food-content">
          <h2 class="title">{{food.name}}</h2>
          <div class="extra">
            <span class="sell">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="new">￥{{food.price}}</span>
            <span class="old">￥{{food.oldPrice}}</span>
          </div>
          <transition name="addcart">
            <span class="addcart" v-show="food.count==0" @click="addFood">加入购物车</span>
          </transition>
          <cartcontrol :food="food" class="cartcontrol"></cartcontrol>
        </div>
        <split></split>
        <div class="food-info" v-show="food.info">
          <h2 class="title">商品介绍</h2>
          <p class="info">{{food.info}}</p>
        </div>
        <split v-show="food.info"></split>
        <div class="food-rating border-1px">
          <h2 class="title">商品评价</h2>
          <ratingselect @updateSelectedType="updateSelectedType" @toggleSwitch="toggleSwitch"
                        :types="['全部','推荐','吐槽']" :selectedType="ratingType" :switchEnable="switchEnable"
                        class="rating-select"></ratingselect>
        </div>
        <ul class="rating-content" v-show="food.ratings && food.ratings.length != 0">
          <li v-for="rating in food.ratings" v-show="showCheck(rating.rateType, rating.text)" class="rating-item border-1px">
            <div class="head">
              <span class="time">{{rating.rateTime | timeFormatter}}</span>
              <span class="avatar">
                <img :src="rating.avatar" width="12px" height="12px">
              </span>
              <span class="username">{{rating.username}}</span>
            </div>
            <div class="content">
              <span class="thumb" :class="[rating.rateType==0?'icon-thumb_up':'icon-thumb_down']" ></span>
              <span>{{rating.text}}</span>
            </div>
          </li>
        </ul>
        <div v-show="!food.ratings || food.ratings.length == 0" class="no-rating">暂无评价</div>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from "../cartcontrol/cartcontrol.vue"
  import split from '../split/split.vue'
  import ratingselect from "../ratingselect/ratingselect.vue"
  require('../../common/js/date')

  const ALL = 2;//POSITIVE = 0;NEGATIVE = 1;

  import BScroll from 'better-scroll'
  export default {
    props: {
      food: Object
    },
    data(){
      return {
        show: false,
        ratingType: ALL,
        switchEnable: true
      }
    },
    methods: {
      addFood(event){
        if(!event._constructed){
            return;
        }
        this.$store.commit('add', this.food);
      },
      toggleShow(event){
        if(!event._constructed){
          return;
        }
        this.show = !this.show;
        if (this.show) {
          this.ratingType = ALL;
          this.switchEnable = true;
          if (!this.detailScroll) {
            this.$nextTick(() => {
              this.detailScroll = new BScroll(document.getElementById('foodDetail'), {
                click: true
              });
            });
          } else {
            this.$nextTick(() => {
              this.detailScroll.refresh();
            })
          }
        }
      },
      updateSelectedType(type){
        this.ratingType = type;
        this.$nextTick(() => {
          this.detailScroll.refresh();
        })
      },
      toggleSwitch(){
        this.switchEnable = !this.switchEnable;
        this.$nextTick(() => {
          this.detailScroll.refresh();
        })
      },
      showCheck(rateType, text){
        if (this.switchEnable && !text) {
          return false;
        }
        if(this.ratingType === ALL){
            return true;
        } else {
            return this.ratingType === rateType;
        }
      }
    },
    filters:{
      timeFormatter: function (value) {
        return new Date(value).Format('yyyy-MM-dd hh:mm');
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .foodDetail
    position: fixed
    left: 0
    top: 0
    bottom: 48px
    width: 100%
    background-color: #fff
    &.foodDetail-enter, &.foodDetail-leave-active
      transform: translateX(100%)
    &.foodDetail-enter-active, &.foodDetail-leave-active
      transition: all 0.5s
    .img-header
      position: relative
      width: 100%
      height: 0
      padding-top: 100%
      img
        position: absolute
        left: 0
        top: 0
        width: 100%
        height: 100%
      .back-hook
        position: absolute
        left: 10px
        top: 10px
        padding: 6px
        font-size: 20px
        color: #fff

    .food-content
      position: relative
      padding: 18px
      .title
        margin-bottom: 8px
        line-height: 14px
        height: 14px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .extra
        lint-height: 10px
        font-size: 10px
        color: rgb(147, 153, 159)
        .sell
          margin-right: 12px
      .price
        margin-top: 18px
        .new, .old
          vertical-align: top
          line-height: 14px
          font-weigth: 700
        .new
          font-size: 14px
          color: rgb(240, 20, 20)
        .old
          font-size: 10px
          color: rgb(147, 153, 159)
          text-decoration: line-through
      .addcart
        position: absolute
        z-index: 10
        right: 18px
        bottom: 18px
        padding: 0 12px
        line-height: 24px
        font-size: 10px
        color: #fff
        background-color: rgb(0, 160, 220)
        border-radius: 12px
        &.addcart-enter,&.addcart-leave-active
          opacity: 0
        &.addcart-enter-active,&.addcart-leave-active
          transition: opacity 0.5s
      .cartcontrol
        position: absolute
        right: 14px
        bottom: 14px

    .food-info
      padding: 18px
      .title
        font-size: 14px
        color: rgb(7, 17, 27)
        margin-bottom: 6px
      .info
        line-height: 24px
        font-size: 12px
        color: rgb(77, 85, 93)

    .food-rating
      .title
        font-size: 14px
        color: rgb(7, 17, 27)
        padding-left: 18px
        padding-top: 18px
    .rating-content
      padding: 0 18px
      .rating-item
        padding: 16px 0
        border-1px(rgba(7,17,27,0.1))
        .head
          line-height: 12px
          font-size: 10px
          color: rgb(147, 153, 159)
          overflow: hidden
          margin-bottom: 6px
          .time
            float: left
          .username, .avatar
            float: right
          .username
            margin-right: 6px
        .content
          line-height: 16px
          font-size: 12px
          color: rgb(7, 17, 27)
          .thumb
            margin-right: 4px
            line-height: 24px
            font-size: 12px
            &.icon-thumb_up
              color:rgb(0,160,220)
            &.icon-thumb_down
              color:rgb(147,153,159)
    .no-rating
      padding: 16px 18px
      text-align: center
      font-size: 12px
      color: rgb(147,153,159)
</style>
