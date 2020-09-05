<template lang="pug">
  .container(
    :style="[ { height: $vuetify.breakpoint.height + 'px' } ]",
  )
    AFrameScene(
      v-if="canLoad"
    )
</template>

<script>
import AFrameScene from '@/components/AFrameScene'
export default {
  head: {
    script: [
      { src: 'https://cdn.jsdelivr.net/npm/hls.js@latest' }
    ]
  },
  components: {
    AFrameScene
  },
  data: () => ({
    canLoad: false
  }),
  mounted () {
    this.checkForHls()
  },
  methods: {
    checkForHls () {
      const { Hls } = window
      if (!Hls) {
        setTimeout(() => {
          this.checkForHls()
        })
      } else {
        this.canLoad = true
      }
    }
  }
}
</script>

<style lang="sass">
.container
  margin: 0 !important
  height: 100%

.title
  display: block
  font-weight: 300
  font-size: 100px
  color: #35495e
  letter-spacing: 1px

.player
  display: none

.subtitle
  font-weight: 300
  font-size: 42px
  color: #526488
  word-spacing: 5px
  padding-bottom: 15px

.links
  padding-top: 15px
</style>
