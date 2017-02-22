<template>
  <div class="seller-wrapper" id="seller-scroll">
    <div>
      <div class="overview">
        <div class="head border-1px">
          <h2 class="title">{{seller.name}}</h2>
          <div class="desc">
            <star :size="36" :score="seller.score" class="star"></star>
            <span class="rating-count">({{seller.ratingCount}})</span>
            <span class="sell-count">月售{{seller.sellCount}}单</span>
          </div>
          <div class="favorite-wrapper" @click="toggleFavorite">
            <i class="icon-favorite" :class="{'favorite':favorite}"></i>
            <span class="collect">{{favoriteText}}</span>
          </div>
        </div>
        <ul class="remark">
          <li class="remark-item">
            <h3>起送价</h3>
            <span>{{seller.minPrice}}元</span>
          </li>
          <li class="remark-item">
            <h3>商家配送</h3>
            <span>{{seller.deliveryPrice}}元</span>
          </li>
          <li class="remark-item">
            <h3>平均配送时间</h3>
            <span>{{seller.deliveryTime}}分</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="bulletin-wrapper">
        <h2 class="title">公告与活动</h2>
        <p class="text">{{seller.bulletin}}</p>
        <ul class="supports">
          <li v-for="support in seller.supports" class="support">
            <support :supportType="support.type" type="4" class="support-icon"></support>
            <span class="description">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics-wrapper">
        <h2 class="title">商家实景</h2>
        <div id="pic-scroll">
          <ul id="pic-ul">
            <li v-for="pic in seller.pics" class="pic-item">
              <img :src="pic" width="120px" height="90px" class="pic">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info-wrapper">
        <h2 class="title">商家信息</h2>
        <ul>
          <li v-for="info in seller.infos" class="info">
            {{info}}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../components/star/star.vue'
  import split from '../split/split.vue'
  import support from '../components/support/support-icon.vue'
  import BScroll from 'better-scroll'
  import {setLocalValue, getLocalValue} from '../../common/js/localStore'

  const FAVORITE = 'favorite';
  export default {
    props: {
      seller: Object
    },
    data(){
      return {
        favorite: (() => {
          return getLocalValue(this.seller.id, FAVORITE, false)
        })()
      }
    },
    updated(){
      if (this.seller) {
        if (this.seller.pics) {
          let el = document.getElementById('pic-scroll').firstChild;
          let marginright = 6;
          let marginleft = 18;
          let picwidth = 120
          let width = (picwidth + marginright) * this.seller.pics.length + marginleft * 2 - marginright;
          el.style.width = width + 'px';
        }
        this.$nextTick(() => {
          this._initScroll();
        })
      }
    },
    computed: {
      favoriteText(){
        return this.favorite ? '已收藏' : '收藏'
      }
    },
    methods: {
      _initScroll(){
        if (!this.sellerScroll) {
          //console.log(document.getElementById('pic-scroll').firstChild.clientWidth)
          this.sellerScroll = new BScroll(document.getElementById('seller-scroll'), {
            click: true
          });
          this.picScroll = new BScroll(document.getElementById('pic-scroll'), {
            scrollX: true,
            eventPassthrough: 'vertical'
          });
        } else {
          this.sellerScroll.refresh();
          this.picScroll.refresh();
        }
      },
      toggleFavorite(event){
        if (!event._constructed) {
          return;
        }
        console.log(1)
        this.favorite = !this.favorite
        setLocalValue(this.seller.id, FAVORITE, this.favorite)
      }
    },
    components: {
      star,
      split,
      support
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .seller-wrapper
    position: fixed
    top: 174px
    left: 0
    bottom: 48px
    width: 100%
    overflow: hidden
    .title
      line-height: 14px
      font-size: 14px
      color: rgb(7, 17, 27)
    .overview
      padding: 18px
      width: 100%
      box-sizing: border-box
      .head
        position: relative
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        .desc
          margin-top: 8px
          font-size: 0
          .star, .rating-count, .sell-count
            display: inline-block
            vertical-align: top
            height: 18px
            line-height: 18px
          .star
            margin-right: 8px
          .rating-count, .sell-count
            font-size: 10px
            color: rgb(77, 85, 93)
          .rating-count
            margin-right: 12px
        .favorite-wrapper
          position: absolute
          right: 0
          top: 0
          width: 40px
          text-align: center
          .icon-favorite
            display: block
            font-size: 24px
            line-height: 24px
            color: rgb(147, 153, 159)
            &.favorite
              color: rgb(220, 20, 20)
          .collect
            font-size: 10px
            color: rgb(77, 85, 93)
      .remark
        display: flex
        .remark-item
          display: inline-block
          flex: 1
          margin-top: 18px
          text-align: center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border-none()
          h3
            margin-bottom: 4px
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          span
            line-height: 24px
            font-size: 20px
            color: rgb(7, 17, 27)

    .bulletin-wrapper
      padding: 18px 18px 0 18px
      .text
        margin: 8px 12px 16px 12px
        line-height: 24px
        font-size: 12px
        color: rgb(240, 20, 20)
      .supports
        .support
          padding: 16px 12px
          border-top: 1px solid rgba(7, 17, 27, 0.1)
          .support-icon, .description
            line-height: 16px
            vertical-align: middle
          .description
            font-size: 12px
            color: rgb(7, 17, 27)
    .pics-wrapper
      padding-bottom: 18px
      .title
        margin-top: 18px
        margin-left: 18px
        margin-bottom: 12px
      #pic-scroll
        width: 100%
        overflow: hidden
        /*white-space: nowrap*/
        .pic-item
          display: inline-block
          margin-right: 6px
          &:first-child
            margin-left: 18px
          &:last-child
            margin-right: 18px
    .info-wrapper
      padding: 18px 18px 0 18px
      .info
        padding: 16px 12px
        line-height: 16px
        font-size: 12px
        color: rgb(7, 17, 27)
        border-top: 1px solid rgba(7, 17, 27, 0.1)
        &:first-child
          margin-top: 12px
</style>
