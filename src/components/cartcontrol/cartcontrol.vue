<template>
  <div class="cartcontrol">
    <span class="decrease" v-show="food.count>0" @click.prevent.stop="remove">
      <i class="icon-remove_circle_outline"></i>
    </span>
    <span class="num" v-show="food.count>0">{{food.count}}</span>
    <span class="add" @click.prevent.stop="add">
      <i class="icon-add_circle"></i>
    </span>
  </div>
</template>

<script type="text/ecmascript-6">
  import ballfly from '../../common/js/ballfly'
  export default {
    props: {
      food: Object,
      drop: {
        type: Boolean,
        default: false
      }
    },
    computed:{
      dropTarget(){
          if(this.drop){
              return document.querySelector('#droptarget').getBoundingClientRect();
          }
          return null;
      }
    },
    methods: {
      add(event){
        if (!event._constructed) {
          return;
        }
        this.$store.commit('add',this.food);
        if(this.drop){
            ballfly(event,this.dropTarget);
        }
      },
      remove(){
        if (!event._constructed) {
          return;
        }
        this.$store.commit('sub',this.food);
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import 'ballfly.css'
  .cartcontrol
    font-size: 0
    line-height: 24px
    .decrease, .add
      vertical-align: middle
      padding: 4px
      display: inline-block
      font-size: 24px
      line-height: 24px
      color: rgb(0, 160, 220)
    .num
      display: inline-block
      //width: 24px
      //padding: 0 6px
      font-size: 10px
      line-height: 24px
      text-align: center
      vertical-align: middle
      color: rgb(147, 153, 159)
</style>
