<template lang="pug">
  a-scene(v-pre)
    a-assets
      img(
        id="sky",
        src="/sky.jpg"
      )
      img(
        id="poster",
        src="/poster.png"
      )
      video(
        id="video",
        crossorigin="anonymous"
      )
      video(
        id="background",
        crossorigin="anonymous"
      )
    a-sky(
      id="local"
      src="#sky"
    )
    a-entity(
      position="0 0 0",
      rotation="0 -90 0",
      v-pre
    )
      a-camera(
        position="0 0 0",
        rotation="0 0 0",
        near="0.5",
        v-pre
      )
        a-entity(
          cursor="fuse: false; fuseTimeout: 500",
          position="0 0 -1",
          geometry="primitive: ring; radiusInner: 0.02; radiusOuter: 0.03",
          id="cursor",
          v-pre
        )
    a-entity(
      id="playback",
      play-background,
      geometry="primitive: plane; width: 1.8; height: 3",
      position="0 0 6.176",
      rotation="0 180 0",
      material="src: #poster",
      v-pre
    )
    a-entity(
      id="projector",
      position="2 0.6 0.41",
      rotation="0 -91 0",
      play-projection,
      v-pre
    )
      a-plane(
        width="2.2",
        height="1.4",
        src="#video",
        event-set__loaded="_delay: 350",
        v-pre
      )
</template>

<script>
import 'aframe'
export default {
  data: () => ({
    backgroundIsPlaying: false,
    videoIsPlaying: false,
    Hls: null,
    AFRAME: null,
    background: null,
    backgroundHls: null,
    backgroundSrc: 'https://bitmovin-a.akamaihd.net/content/playhouse-vr/m3u8s/105560.m3u8',
    video: null,
    videoHls: null,
    videoSrc: 'https://bitdash-a.akamaihd.net/content/sintel/hls/playlist.m3u8',
  }),
  mounted () {
    const { Hls, AFRAME } = window
    this.Hls = Hls
    this.AFRAME = AFRAME
    this.video = document.getElementById('video');
    this.background = document.getElementById('background');
    const self = this
    this.playVideo()
    this.registerPlayBack()
    this.registerProjector()
  },
  methods: {
    registerPlayBack () {
      const self = this
      this.AFRAME.registerComponent('play-background', {
        init: function () {
          this.el.addEventListener('click', function (evt) {
            if (!this.backgroundIsPlaying) {
              self.playBackground()
            }
          })
          this.el.addEventListener('mouseenter', function (evt) {
            this.setAttribute('scale', { x: 1.1, y: 1.1, z: 1.1 })
          })
          this.el.addEventListener('mouseleave', function (evt) {
            this.setAttribute('scale', { x: 1, y: 1, z: 1 })
          })
        }
      })
    },
    registerProjector () {
      const self = this
      this.AFRAME.registerComponent('play-projection', {
        init: function () {
          this.el.addEventListener('click', function (evt) {
            if (!this.videoIsPlaying) {
              self.playVideo()
            }
          })
        }
      })
    },
    playVideo () {
      if (Hls.isSupported()) {
        this.videoHls = new this.Hls()
        this.videoHls.loadSource(this.videoSrc)
        this.videoHls.attachMedia(this.video)
        this.videoHls.on(this.Hls.Events.MANIFEST_PARSED, () => {
          this.video.play()
          this.videoIsPlaying = true
        });
      } else if (this.video.canPlayType('application/vnd.apple.mpegurl')) {
        this.video.src = this.videoSrc;
        this.video.addEventListener('loadedmetadata', () => {
          this.video.play()
          this.videoIsPlaying = true
        })
      }
    },
    playBackground () {
      this.video.pause()
      const sky = document.querySelector('#local')
      const projector = document.querySelector('#projector')
      const poster = document.querySelector('#playback')
      sky.setAttribute('src', '#background')
      projector.setAttribute('visible', false)
      poster.setAttribute('visible', false)
      if (Hls.isSupported()) {
        this.backgroundHls = new this.Hls()
        this.backgroundHls.loadSource(this.backgroundSrc)
        this.backgroundHls.attachMedia(this.background)
        this.backgroundHls.on(this.Hls.Events.MANIFEST_PARSED, () => {
          this.background.play()
          this.backgroundIsPlaying = true
        });
      } else if (this.background.canPlayType('application/vnd.apple.mpegurl')) {
        this.background.src = this.backgroundSrc;
        this.background.addEventListener('loadedmetadata', () => {
          this.background.play()
          this.backgroundIsPlaying = true
        })
      }
    }
  }
}
</script>
