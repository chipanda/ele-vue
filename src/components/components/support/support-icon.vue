<template>
  <span :class="[classMap[supportType], sizeType]" class="support-icon"></span>
</template>

<script type="text/ecmascript-6">
  export default {
      props: {
          supportType: Number,
          type: String
      },
      created() {
          this.classMap = ["decrease","discount","special","invoice","guarantee"]
      },
      computed: {
          sizeType() {
              return "support-" + this.type;
          }
      }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../../common/stylus/mixin.styl"

  .support-icon
    display:inline-block
    &.support-1
      bg-size(12px,12px)
      bg-support("_1")
    &.support-2
      bg-size(16px,16px)
      bg-support("_2")
    &.support-3
      bg-size(12px,12px)
      bg-support("_3")
    &.support-4
      bg-size(16px,16px)
      bg-support("_4")
</style>
