<template>
  <div id="apps">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
    <div class="shopcart-wrapper">
      <shopcart ref="shopcart" :seller="seller" :selectedFoods="selectedFoods"></shopcart>
    </div>
  </div>
</template>

<script>
  import header from './components/header/header'
  import Vue from 'vue'
  import Vuex, {mapState, mapGetters, mapMutations} from "vuex"
  import shopcart from "./components/shopcart/shopcart.vue"
  import {urlParse} from './common/js/util'

  Vue.use(Vuex);

  const ERR_OK = 0;
  const store = new Vuex.Store({
    state: {
      goods: []
    },
    getters: {
      selectedFoods(state){
        let foods = [];
        state.goods.forEach((category) => {
          category.foods.forEach((food) => {
            if (food.count !== 0) {
              foods.push(food);
            }
          })
        })
        return foods;
      }
    },
    mutations: {
      initGoods(state, payload){
        state.goods = payload.goods;
        state.goods.forEach((category) => {
          category.foods.forEach((food) => {
            Vue.set(food, 'count', 0)
          })
        })
      },
      add(state, food){
        food.count++;
      },
      sub(state, food){
        if (food.count) {
          food.count--;
        }
      },
      empty(state, getters){
        getters.selectedFoods.forEach((food) => {
          food.count = 0;
        })
      }
    },
    actions: {}
  })

  export default {
    name: 'app',
    store,
    data() {
      return {
        seller: {
          id: (() => {
            let param = urlParse();
            return param.id
          })()
        },
      }
    },
    computed: {
      ...mapGetters(['selectedFoods'])
    },
    created() {
      this.$http.get('/api/seller'+'?id='+this.seller.id).then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.seller = Object.assign({}, this.seller, response.data);
        }
      })
    },
    components: {
      'v-header': header,
      shopcart
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/mixin.styl"

  .tab
    display flex
    width 100%
    height 40px
    line-height 40px
    border-1px(rgba(7, 17, 27, 0.1))
    .tab-item
      flex 1px
      text-align center
      & > a
        display block
        font-size 14px
        color rgb(77, 85, 93)
        &.active
          color rgb(240, 20, 20)
</style>
