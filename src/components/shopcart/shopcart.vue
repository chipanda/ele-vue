<template>
  <div class="shopcart">
    <div class="content" @click="toggleShowList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{active: totalCount>0}" id="droptarget">
            <i class="icon-shopping_cart"></i>
            <span v-show="totalCount>0" class="total-count">{{totalCount}}</span>
          </div>
        </div>
        <div class="detail">
          <div class="price" :class="{active: totalPrice>0}">￥{{totalPrice}}</div>
          <div class="delivery">另需配送费{{seller.deliveryPrice}}元</div>
        </div>
      </div>
      <div class="content-right">
        <div class="pay-wrapper" :class="{active: enough}">
          {{payDesc}}
        </div>
      </div>
    </div>
    <!--<transition-group-->
    <!--v-on:before-enter="ballBeforeEnter"-->
    <!--v-on:enter="ballEnter"-->
    <!--v-on:after-enter="ballAfterEnter"-->
    <!--name="ball"-->
    <!--class="ball-container"-->
    <!--tag="div"-->
    <!--:css="false" >-->
    <!--<div v-for="(ball,$index) in balls" v-show="ball.show" :key="$index" class="ball">-->
    <!--<div class="inner-wrapper">-->
    <!--<div class="inner"></div>-->
    <!--</div>-->

    <!--</div>-->
    <!--</transition-group>-->
    <transition name="shopcart-list">
      <div v-show="listShow" class="shop-list">
        <div class="header">
          <div class="title">购物车</div>
          <div class="empty" @click.prevent.stop="empty">清空</div>
        </div>
        <div id="list-scroll" class="foods">
          <ul>
            <li v-for="food in selectedFoods" class="food">
              <span class="name">{{food.name}}</span>
              <span class="price">￥{{food.price*food.count}}</span>
              <cartcontrol :food="food" class="cartcontrol-wrapper"></cartcontrol>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="shopcart-bg">
      <div class="shopcart-bg" v-show="listShow" @click="toggleShowList"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import {mapGetters} from "vuex"
  import cartcontrol from"../cartcontrol/cartcontrol.vue"
  import BScroll from "better-scroll"

  export default {
    props: {
      seller: Object
    },
    data(){
      return {
        fold: true,
        balls: [
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false}
        ],
        dropBalls: []
      }
    },
    computed: {
      ...mapGetters(['selectedFoods']),
      totalPrice(){
        let total = 0;
        this.selectedFoods.forEach((food) => {
          total += food.price * food.count;
        })
        return total;
      },
      totalCount(){
        let total = 0;
        this.selectedFoods.forEach((food) => {
          total += food.count;
        })
        return total;
      },
      payDesc(){
        if (this.totalPrice === 0) {
          return `￥${this.seller.minPrice}起送`;
        } else if (this.totalPrice < this.seller.minPrice) {
          let diff = this.seller.minPrice - this.totalPrice;
          return `还差￥${diff}元`;
        } else {
          return "结算";
        }
      },
      enough(){
        return this.totalPrice >= this.seller.minPrice;
      },
      listShow(){
        if (!this.totalCount) {
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.listScroll) {
              this.listScroll = new BScroll(document.getElementById('list-scroll'), {
                click: true
              })
            } else {
              this.listScroll.refresh();
            }
          })
        }
        return show;
      }
    },
    methods: {
      toggleShowList(){
        if (this.totalCount) {
          this.fold = !this.fold;
        }
      },
      empty(){
        this.$store.commit('empty', this.$store.getters);
        this.fold = true;
      }
//      ballBeforeEnter(el){
//        let ball = this.dropBalls[this.dropBalls.length-1];
//        let rect = ball.el.getBoundingClientRect();
//        let x = rect.left - 32;
//        let y = -(window.innerHeight - rect.top - 22);
//        console.log(x);
//        console.log(rect.top);
////
////        let innerEle = el.getElementsByClassName("inner")[0];
////        el.style.webkitTransform = `translateX(${x}px)`;
////        el.style.transform = `translateX(${x}px)`;
////        innerEle.style.webkitTransform = `translateY(${y}px)`;
////        innerEle.style.transform = `translateY(${y}px)`;
//        el.style.top = `${rect.top}px`;
//        el.style.left = `${rect.left}px`;
//        //let wrapperEle = el.getElementsByClassName("inner-wrapper")[0];
//        //wrapperEle.style.webkitTransform = `translateY(${y}px)`;
//       // wrapperEle.style.transform = `translateY(${y}px)`;
//        let innerEle = el.getElementsByClassName("inner")[0];
//        innerEle.style.webkitTransform = `translateX(${x}px)`;
//        innerEle.style.transform = `translateX(${x}px)`;
//        el.style.display = '';
//      },
//      ballEnter(el,done){
//          done();
//        //触发浏览器重绘
//        //let rf = el.offsetHeight;
//        //this.$nextTick(()=>{
////          el.style.webkitTransform = 'translateY(0)';
////          el.style.transform = 'translateY(0)';
////          let innerEle = el.getElementsByClassName("inner")[0];
////          innerEle.style.webkitTransform = 'translateX(0)';
////          innerEle.style.transform = 'translateX(0)';
//          //done();
//        //})
//      },
//      ballAfterEnter(el){
//        let ball = this.dropBalls.shift();
////        if(ball) {
////          ball.show = false;
////          ball.el = null;
////        }
//
//      },
//      drop(el){
//        for(let i=0;i<this.balls.length;i++){
//          let ball = this.balls[i];
//          if(!ball.show) {
//            ball.show = true;
//            ball.el = el;
//            this.dropBalls.push(ball);
//            return;
//          }
//        }
//      }
    },
    components: {
      cartcontrol
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position: fixed
    width: 100%
    height: 48px
    left: 0
    bottom: 0
    z-index:50;
    .content
      display: flex
      width: 100%
      height: 48px
      background-color: #141d27
      color: rgba(255, 255, 255, 0.4)
      .content-left
        flex: auto
        height: 100%
        .logo-wrapper
          float: left
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          border-radius: 50%
          background-color: #141d27
          .logo
            width: 44px
            height: 44px
            text-align center
            border-radius: 50%
            background-color: rgba(255, 255, 255, 0.2)
            &.active
              color: #fff
              background-color: rgb(0, 160, 220)
            .icon-shopping_cart
              height: 44px
              line-height: 44px
              font-size: 24px
            .total-count
              position: absolute
              top: 0
              left: 32px
              min-width: 24px
              height: 16px;
              line-height: 16px
              font-size: 9px
              font-weight: 700
              text-align: center
              color: rgb(255, 255, 255)
              background-color: rgb(240, 20, 20)
              border-radius: 12px
              box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .detail
          float: left
          padding: 12px 0
          font-size: 0
          .price, .delivery
            display: inline-block
            height: 24px
            line-height: 24px
          .price
            vertical-align: top
            font-size: 16px
            font-weight: 700
            padding-right: 12px
            border-right: 1px solid rgba(255, 255, 255, 0.1)
            &.active
              color: #fff
          .delivery
            font-size: 10px
            padding-left: 12px
      .content-right
        flex: none
        width: 105px
        heigth: 48px
        .pay-wrapper
          width: 100%
          line-height: 48px
          font-size: 14px
          text-align: center
          background-color: rgba(255, 255, 255, 0.2)
          &.active
            background-color: #00b43c
            color: #fff
    .shop-list
      position: absolute
      left: 0
      top: 0
      z-index: -1
      width: 100%
      transform: translateY(-100%)
      &.shopcart-list-enter, &.shopcart-list-leave-active
        transform: translateY(0)
      &.shopcart-list-enter-active, &.shopcart-list-leave-active
        transition: all 0.3s linear
      .header
        line-height: 40px
        height: 40px
        background-color: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        overflow: hidden
        .title
          float: left
          margin-left: 18px
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          margin-right: 18px
          font-size: 12px
          color: rgb(0, 160, 220)
      .foods
        max-height: 217px
        overflow: hidden
        padding: 0 18px
        background-color: #fff
        .food
          position: relative
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          .name
            display: inline-block
            font-size: 14px
            line-height: 48px
            color: rgb(7, 17, 27)
          .price
            display: inline-block
            float: right
            margin-right: 84px
            line-height: 48px
            font-size: 12px
            font-weight: 700
            color: rgb(240, 20, 20)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 16px
            height: 24px
    .shopcart-bg
      position: fixed
      top: 0
      left: 0
      z-index: -10
      width: 100%
      height: 100vh
      background-color: rgba(7,17,27,0.6)
      opacity: 1
      &.shopcart-bg-enter, &.shopcart-bg-leave-active
        opacity: 0
      &.shopcart-bg-enter-active, &.shopcart-bg-leave-active
        transition: all 0.3s
</style>
