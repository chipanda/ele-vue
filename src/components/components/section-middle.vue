<template>
<div class="title-wrapper">
  <span class="line"></span>
  <span class="title">{{title}}</span>
  <span class="line"></span>
</div>
</template>

<script type="text/ecmascript-6">
  export default {
      props: {
          title: String
      }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .title-wrapper
    display: flex
    flex-direction: row
    margin-top: 28px
    margin-bottom: 24px
    .line
      flex: auto
      margin-top: -7px
      width:auto
      height: 14px
      border-bottom: 1px solid rgba(255,255,255,0.2)
    .title
      flex: none
      padding: 0 12px
      font-size: 14px
      line-height: 14px
      height: 14px
      font-weight: 700

</style>
