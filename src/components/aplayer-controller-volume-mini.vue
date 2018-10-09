<template>
  <div class="aplayer-volume-wrap-mini aplayer-mini-volume-wrap-mini" style="position: absolute; right: -24px; bottom: 13px;">
    <icon-button
      :class="`aplayer-icon-${volumeIcon}`"
      :icon="volumeIcon"
      @click.native="$emit('togglemute')"
    />
    <div
      class="aplayer-volume-bar-wrap-mini"
      @mousedown="onBarMouseDown"
    >
      <div class="aplayer-volume-bar-mini" ref="bar">
        <div
          class="aplayer-volume-mini"
          :style="{
            height: muted ? 0 : `${Math.trunc(volume * 100)}%`,
            background: '#d54e4a'
          }"
        >
        </div>
      </div>
    </div>
  </div>

</template>

<script>
  import IconButton from './aplayer-iconbutton.vue'
  import {getElementViewTop} from '../utils'

  const barHeight = 40

  export default {
    components: {
      IconButton,
    },
    props: ['volume', 'muted', 'theme'],
    computed: {
      volumeIcon () {
        if (this.muted || this.volume <= 0) return 'volume-off'
        if (this.volume >= 1) return 'volume-up'
        return 'volume-down'
      },
    },
    methods: {
      adjustVolume (e) {
        let percentage = (barHeight - e.clientY + getElementViewTop(this.$refs.bar)) / barHeight
        percentage = percentage > 0 ? percentage : 0
        percentage = percentage < 1 ? percentage : 1
        this.$emit('setvolume', percentage)
      },
      onBarMouseDown () {
        document.addEventListener('mousemove', this.onDocumentMouseMove)
        document.addEventListener('mouseup', this.onDocumentMouseUp)
      },
      onDocumentMouseMove (e) {
        let percentage = (barHeight - e.clientY + getElementViewTop(this.$refs.bar)) / barHeight
        percentage = percentage > 0 ? percentage : 0
        percentage = percentage < 1 ? percentage : 1
        this.$emit('setvolume', percentage)
      },
      onDocumentMouseUp (e) {
        document.removeEventListener('mouseup', this.onDocumentMouseUp)
        document.removeEventListener('mousemove', this.onDocumentMouseMove)

        let percentage = (barHeight - e.clientY + getElementViewTop(this.$refs.bar)) / barHeight
        percentage = percentage > 0 ? percentage : 0
        percentage = percentage < 1 ? percentage : 1
        this.$emit('setvolume', percentage)
      }
    }
  }
</script>

<style lang="scss">

  .aplayer-volume-wrap-mini {
    position: relative;
    cursor: pointer;
    z-index: 0;

    &:hover .aplayer-volume-bar-wrap-mini {
      display: block;
    }

    .aplayer-volume-bar-wrap-mini {
      position: absolute;
      bottom: 15px;
      left: -4px;
      right: -4px;
      height: 40px;
      z-index: -1;
      transition: all .2s ease;

      &::after {
        content: '';
        position: absolute;
        bottom: -16px;
        left: 0;
        right: 0;
        height: 68px;
        /*background-color: rgba(0, 0, 0, 0.2);*/
        /*box-shadow: 0 0 2px 0 rgba(0, 0, 0, 0.07), 0 0 5px 0 rgba(0, 0, 0, 0.1);*/
      }

      .aplayer-volume-bar-mini {
        position: absolute;
        bottom: 7px;
        left: 13px;
        width: 5px;
        height: 40px;
        background: #ffffff;
        border-radius: 2.5px;
        overflow: hidden;
        z-index: 1;

        .aplayer-volume-mini {
          position: absolute;
          bottom: 0;
          left: 0;
          right: 0;
          transition: height 0.1s ease, background-color .3s;
          will-change: height;
        }
      }
    }
  }
  .aplayer-mini-volume-wrap-mini .aplayer-volume-bar-wrap-mini::after{
    /*background: rgba(255, 255, 255, 0.48);*/
  }
.aplayer-mini-volume-wrap-mini .aplayer-volume-bar-wrap-mini{
    /*display: block!important;*/
  }
  .aplayer-pic-mini{
    border-radius: 56px;
  }
</style>