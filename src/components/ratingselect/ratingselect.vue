<template>
  <div class="rating-select">
    <div class="rating-type border-1px">
      <span class="positive" :class="{'active':ALL === selectedType}" @click="selectType(ALL,$event)">{{types[0]}}</span>
      <span class="positive" :class="{'active':POSITIVE === selectedType}" @click="selectType(POSITIVE,$event)">{{types[1]}}</span>
      <span class="negative" :class="{'active':NEGATIVE === selectedType}" @click="selectType(NEGATIVE,$event)">{{types[2]}}</span>
    </div>
    <div class="switch">
      <i class="icon-check_circle" :class="{enable:switchEnable}" @click="toggleSwitch"></i>
      <span class="switch-text">只看有评价的内容</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const ALL = 2;
  const POSITIVE = 0;
  const NEGATIVE = 1;

  export default {
    props: {
      types: {
        type: Array,
        default(){
          return ['全部', '好评', '差评']
        }
      },
      selectedType: {
        type: Number,
        default: 0
      },
      switchEnable: {
        type: Boolean,
        default: true
      }
    },
    data(){
      return {
        ALL: ALL,
        POSITIVE: POSITIVE,
        NEGATIVE: NEGATIVE
      }
    },
    methods: {
      selectType(type, event){
        if (!event._constructed) {
          return;
        }
        this.$emit('updateSelectedType', type);
      },
      toggleSwitch(){
        if (!event._constructed) {
          return;
        }
        this.$emit('toggleSwitch');
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .rating-select
    width: 100%
    padding: 18px 18px 0 18px
    box-sizing: border-box
    border-1px(rgba(7, 17, 27, 0.1))
    .rating-type
      font-size: 0
      padding-bottom: 18px
      border-1px(rgba(7, 17, 27, 0.1))
      .positive, .negative
        display: inline-block
        padding: 8px
        text-align: center
        font-size: 12px
        border-radius: 6px
        color: rgb(77, 85, 93)
        &.active
          color: #fff
      .positive
        margin-right: 8px
        background-color: rgba(0, 160, 220, 0.2)
        &.active
          background-color: rgb(0, 160, 220)
      .negative
        background-color: rgba(7, 17, 27, 0.2)
        &.active
          background-color: rgba(7, 17, 27, 0.7)
    .switch
      padding: 12px 0
      line-height: 24px
      color: rgb(147, 153, 159)
      .icon-check_circle
        margin-left: -4px
        padding: 4px
        font-size: 24px
        vertical-align: middle
        &.enable
          color: greenyellow
      .switch-text
        font-size: 12px
</style>
