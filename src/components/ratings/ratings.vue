<template>
  <div class="ratings-wrapper" id="rating-scroll-wrapper">
    <div>
      <div class="overview">
        <div class="left">
          <span class="score">{{seller.score}}</span>
          <span class="desc">综合评分</span>
          <span class="rank">高于周边商家{{seller.rankRate}}%</span>
        </div>
        <div class="right">
          <dl class="overview-detail">
            <dt>服务态度</dt>
            <dd>
              <star :score="seller.serviceScore" :size="36" class="star"></star>
              <span>{{seller.serviceScore}}</span>
            </dd>
            <br>
            <dt>食物评分</dt>
            <dd>
              <star :score="seller.score" :size="36" class="star"></star>
              <span>{{seller.score}}</span>
            </dd>
            <br>
            <dt>送达时间</dt>
            <dd>
              <span class="delivery-time">{{seller.deliveryTime}}分钟</span>
            </dd>
          </dl>
        </div>
      </div>
      <split></split>
      <ratingselect @updateSelectedType="updateSelectedType" @toggleSwitch="toggleSwitch"
                    :selectedType="ratingType" :switchEnable="switchEnable"
                    class="rating-select"></ratingselect>
      <ul class="ratings">
        <li v-for="rating in ratings" v-show="showCheck(rating.rateType, rating.text)" class="rating-item border-1px">
          <div class="avatar">
            <img width="28px" height="28px" :src="rating.avatar">
          </div>
          <div class="content">
            <div class="name">{{rating.username}}</div>
            <div>
              <star :score="rating.score" :size="24" class="star"></star>
              <span class="delivery-time" v-show="rating.deliveryTime">{{rating.deliveryTime}}分送达</span>
            </div>
            <div class="text">
              {{rating.text}}
            </div>
            <div class="recommend">
              <span :class="[rating.rateType==0?'icon-thumb_up':'icon-thumb_down']"></span>
              <span v-for="item in rating.recommend" class="recommend-text">{{item}}</span>
            </div>
            <div class="ratingtime">{{rating.rateTime | timeFormatter}}</div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import split from "../split/split.vue"
  import ratingselect from '../ratingselect/ratingselect.vue'
  import star from '../components/star/star.vue'
  import BScroll from "better-scroll"
  import date from '../../common/js/date'

  const ERR_OK = 0;
  const ALL = 2;
  export default {
    props: {
      seller: Object
    },
    data(){
      return {
        ratings: [],
        ratingType: ALL,
        switchEnable: true
      }
    },
    mounted(){
      this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          this.$nextTick(() => {
            this._initScroll();
          })
        }
      })
    },
    methods: {
      _initScroll(){
        if (!this.ratingScroll) {
          this.ratingScroll = new BScroll(document.getElementById('rating-scroll-wrapper'), {
            click: true
          })
        } else {
          this.ratingScroll.refresh();
        }
      },
      updateSelectedType(type){
        this.ratingType = type;
        this.$nextTick(() => {
          this.ratingScroll.refresh();
        })
      },
      toggleSwitch(){
        this.switchEnable = !this.switchEnable;
        this.$nextTick(() => {
          this.ratingScroll.refresh();
        })
      },
      showCheck(rateType, text){
        if (this.switchEnable && !text) {
          return false;
        }
        if (this.ratingType === ALL) {
          return true;
        } else {
          return this.ratingType === rateType;
        }
      }
    },
    filters:{
      timeFormatter(value){
         return new Date(value).Format("yyyy-MM-dd hh:mm");
      }
    },
    components: {
      split,
      ratingselect,
      star
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .ratings-wrapper
    position: fixed
    left: 0
    top: 174px
    bottom: 48px
    width: 100%
    overflow: hidden
    .overview
      display: flex
      width: 100%
      padding: 18px 0
      .left
        flex: 1 1 275px
        width: 275px
        text-align: center
        span
          display: block
        .score
          margin-bottom: 6px
          line-height: 28px
          font-size: 24px
          color: rgb(255, 153, 0)
        .desc
          margin-bottom: 8px
          line-height: 12px
          font-size: 12px
          color: rgb(7, 17, 27)
        .rank
          line-height: 10px
          font-size: 10px
          color: rgb(147, 153, 159)
      .right
        flex: 1 1 474px
        width: 474px
        box-sizing: border-box
        padding: 0 24px
        font-size: 12px
        .overview-detail
          dt, dd
            display: inline-block
            vertical-align: middle
            height: 18px
            line-height: 18px
            margin-bottom: 8px
          dt
            margin-right: 12px
          dd
            font-size: 0px
            color: rgb(255, 153, 0)
            span
              font-size: 12px
          .star
            display: inline-block
            vertical-align: top
            margin-right: 12px
            line-height: 18px
            height: 18px
          .delivery-time
            color: rgb(147, 153, 159)

    .ratings
      padding: 0 16px
      .rating-item
        display: flex
        padding: 16px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex: none
          width: 28px
          height: 28px
          margin-right: 12px
          border-radius: 50%
          overflow: hidden
        .content
          flex: auto
          .name
            margin-bottom: 4px
            line-height: 12px
            font-size: 10px
            color: rgb(7,17,27)
          .star
            display: inline-block
          .delivery-time
            line-height: 12px
            font-size: 10px
            color:rgb(147,153,159)
          .text
            margin-top: 8px
            line-height: 18px
            font-size: 12px
            color: rgb(7,17,27)
          .recommend
            span
              display: inline-block
              margin-right: 8px
              margin-top: 8px
            .icon-thumb_up
              font-size: 12px
              color:rgb(0,160,220)
            .icon-thumb_down
              font-size: 12px
              color:rgb(147,153,159)
            .recommend-text
              padding: 0 6px
              font-size: 9px
              border: 1px solid rgba(7,17,27,0.1)
              border-radius: 2px
          .ratingtime
            position:absolute
            top:18px
            right:0
            line-height: 12px
            font-size: 10px
            color: rgb(147,153,159)
</style>
