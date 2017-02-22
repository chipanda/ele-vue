<template>
  <ul class="stars" :class="starType">
    <li v-for="star in stars" :class="star" class="star-item"></li>
  </ul>
</template>

<script type="text/ecmascript-6">
  const STAR_COUNT = 5
  const ON = "on"
  const HALF = "half"
  const OFF = "off"

  export default {
    props: {
      score: Number,
      size: Number //24,36,48
    },
    computed: {
      stars() {
        let starClasses = [];
        let score = this.score;
        while (starClasses.length < STAR_COUNT) {
          if (score >= 1) {
            starClasses.push(ON);
            score--;
          } else if (score >= 0.5) {
            starClasses.push(HALF);
            score--;
          } else {
            starClasses.push(OFF);
          }
        }
        return starClasses;
      },
      starType() {
        return 'star-' + this.size;
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../../common/stylus/mixin.styl"
  .stars
    list-style: none
    font-size: 0
    .star-item
      display: inline-block
      &:last-child
        margin-right: 0
    &.star-24
      .star-item
        bg-size(10px, 10px)
        margin-right: 2px
        &:last-child
          margin-right: 0
      .on
        bg-image("star24_on")
      .half
        bg-image("star24_half")
      .off
        bg-image("star24_off")
    &.star-36
      .star-item
        bg-size(15px, 15px)
        margin-right: 6px
        &:last-child
          margin-right: 0
      .on
        bg-image("star36_on")
      .half
        bg-image("star36_half")
      .off
        bg-image("star36_off")
    &.star-48
      .star-item
        bg-size(20px, 20px)
        margin-right: 3px
        &:last-child
          margin-right: 0
      .on
        bg-image("star48_on")
      .half
        bg-image("star48_half")
      .off
        bg-image("star48_off")
</style>
