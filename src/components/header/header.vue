<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar">
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
          <support-icon class="icon" :supportType="seller.supports[0].type" type="1"></support-icon>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showPrompt">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showPrompt">
      <span class="title">
      </span><span class="text">{{seller.bulletin}}</span>
      <span class="icon-keyboard_arrow_right"></span>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
      <div v-show="promptShow"  class="prompt" id="test-scroll">
        <div class="content" >
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :score="seller.score" :size="48"></star>
          </div>
          <section-middle title="优惠信息"></section-middle>
          <ul class="support">
            <li v-for="support in seller.supports" class="support-item">
              <support-icon class="icon" :supportType="support.type" type="2"></support-icon>
              <span class="text">{{support.description}}</span>
            </li>
          </ul>
          <section-middle title="商家信息"></section-middle>
          <div class="bulletin">
            {{seller.bulletin}}
          </div>
        </div>
        <div class="footer" @click="hidePrompt">
          <span class="icon-close"></span>
        </div>
      </div>
    </transition>

  </div>
</template>

<script type="text/ecmascript-6">
  import supportIcon from "../components/support/support-icon.vue"
  import star from "../components/star/star.vue"
  import sectionMiddle from "../components/section-middle.vue"
  export default {
    props: {
      seller: Object
    },
    data() {
      return {
        promptShow: false
      }
    },
    methods: {
      showPrompt() {
        this.promptShow = true
      },
      hidePrompt() {
        this.promptShow = false
      }
    },
    components: {
      "support-icon": supportIcon,
      star,
      "section-middle": sectionMiddle
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .header
    position: relative
    color: #fff
    background-color: rgba(7, 17, 27, 0.5)
    overflow: hidden
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      font-size: 0px
      .avatar
        display: inline-block
        vertical-align: top
        margin-right: 16px
        img
          border-radius: 2px
      .content
        display: inline-block
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            vertical-align: top
            margin-right: 6px
            bg-size(30px, 18px)
            bg-image("brand")
          .name
            line-height: 18px
            font-size: 16px
            font-weight: bold
        .description
          margin-bottom: 10px
          line-height: 12px
          font-size: 12px
        .support
          .icon
            display: inline-block
            vertical-align: top
            margin: 0 4px 0 2px
          .text
            line-height: 12px
            font-size: 10px

      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        line-height: 24px
        font-size: 10px
        border-radius: 14px
        background-color: rgba(0, 0, 0, 0.2)
        .count
          vertical-align: top
        .icon-keyboard_arrow_right
          line-height: 24px

    .bulletin-wrapper
      position: relative
      height: 28px
      line-height: 28px
      padding: 0 22px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      background-color: rgba(7, 17, 27, 0.2)
      .title
        display: inline-block
        margin-top 7px
        vertical-align: top
        bg-size(22px, 12px)
        bg-image("bulletin")
      .text
        vertical-align: top
        margin: 0 4px
        font-size: 10px
        line-height: 28px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 12px
        top: 8px
    .background
      position: absolute
      top: 0
      left: 0
      filter: blur(10px)
      z-index: -1
      width: 100%
      height: 100%
    .prompt
      display: flex
      flex-direction: column
      position: fixed
      z-index: 1000
      top: 0
      left: 0
      width: 100%
      height: 100%
      background-color: rgba(7, 17, 27, 0.8)
      overflow: auto
      //backdrop-filter: blur(10px) //iphone生效
      &.fade-enter, &.fade-leave-active
        opacity: 0
      &.fade-enter-active, &.fade-leave-active
        transition: all 0.5s
      .content
        flex: 1 1 auto
        width: auto
        padding-top: 60px
        padding-left: 36px
        padding-right: 36px
        .name
          margin-bottom: 16px
          text-align: center
          font-size: 16px
          font-weight: 700
          line-height: 16px
        .star-wrapper
          text-align: center
        .support
          .support-item
            font-size: 0
            margin-bottom: 12px
            .icon
              display: inline-block
              vertical-align: top
              margin: 0 4px 0 2px
            .text
              height: 16px
              line-height: 16px
              font-size: 12px
        .bulletin
          padding: 0 12px
          line-height: 24px
          font-size: 12px
      .footer
        flex: none
        height: 64px
        width: 100%
        text-align: center
        .icon-close
          font-size: 32px
          color: rgba(255, 255, 255, 0.5)

</style>
