<template>
  <div>
  <transition name="slide-v">
    <div
      class="aplayer-list"
      :style="listHeightStyle"
      ref="list"
      v-show="show"
    >
      <ol
        ref="ol"
        :style="listHeightStyle"
      >
        <li
          v-for="(aMusic, index) of listLinks[currentLink]"
          :key="index"
          :class="{'aplayer-list-light': aMusic === currentMusic}"
          @click="$emit('selectsong', aMusic)"
        >
          <span class="aplayer-list-cur" :style="{background: theme}"></span>
          <span class="aplayer-list-index">{{(currentLink*songsPerPage) + (index + 1)}}</span>
          <span class="aplayer-list-title">{{ aMusic.title || 'Untitled' }}</span>
          <span class="aplayer-list-author">{{ aMusic.artist || 'Unknown' }}</span>
        </li>
      </ol>
    </div>
  </transition>
    <div style="margin: 0px auto; display: table;">
    <button type="button" v-if="!miniMode && currentLink > 0" @click="prevPage()" class="btn btn-danger" style="margin-right: 5px;">Предыдущая</button>
    <button type="button" v-if="!miniMode && currentLink < songPerPages-1" @click="nextPage()" class="btn btn-danger" style="margin-right: 5px;">Следующая</button>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      songsPerPage:{
        type: Number,
        default: 0,
      },
      miniMode:{
        type: Boolean,
        default: false,
      },
      show: {
        type: Boolean,
        default: true,
      },
      currentMusic: Object,
      musicList: {
        type: Array,
        default () {
          return []
        }
      },
      playIndex: {
        type: Number,
        default: 0,
      },
      theme: String,
      listmaxheight: String,
    },
    data (){
      return{
        listLinks: [],
        currentLink: 0,
        songPerPages: 0,
      }
    },
    created (){
      if(this.musicList.length){
        this.songPerPages = Math.round(this.musicList.length/this.songsPerPage);
        for (let link = 0; link < this.songPerPages; link++) {
          this.listLinks[link] = [];
            for (let song = (link*this.songsPerPage); song < (link*this.songsPerPage + this.songsPerPage); song++) {
              this.listLinks[link].push(this.musicList[song]);
            }
        }
      }
    },
    methods:{
      nextPage (){
        this.currentLink++;
      },
      prevPage (){
        this.currentLink--;
      },
    },
    computed: {
      listHeightStyle () {
        return {
          height: `${33 * this.songsPerPage + 5}px`,
          maxHeight: this.listmaxheight || ''
        }
      }
    }
  }
</script>

<style lang="scss">

  .aplayer-list {
    overflow: hidden;

    &.slide-v-enter-active,
    &.slide-v-leave-active {
      transition: height 500ms ease;
      will-change: height;
    }

    &.slide-v-enter,
    &.slide-v-leave-to {
      height: 0 !important;
    }

    ol {
      list-style-type: none;
      margin: 0;
      padding: 0;
      overflow-y: auto;

      &::-webkit-scrollbar {
        width: 5px;
      }

      &::-webkit-scrollbar-track {
        background-color: #f9f9f9;
      }

      &::-webkit-scrollbar-thumb {
        border-radius: 3px;
        background-color: #eee;
      }

      &::-webkit-scrollbar-thumb:hover {
        background-color: #ccc;
      }

      &:hover {
        li.aplayer-list-light:not(:hover) {
          background-color: inherit;
          transition: inherit;
        }
      }

      &:not(:hover) {
        li.aplayer-list-light {
          transition: background-color .6s ease;
        }
      }
      li {
        position: relative;
        height: 32px;
        line-height: 32px;
        padding: 0 15px;
        font-size: 12px;
        border-top: 1px solid #e9e9e9;
        cursor: pointer;
        transition: all 0.2s ease;
        overflow: hidden;
        margin: 0;
        text-align: start;
        display: flex;

        &:first-child {
          border-top: none;
        }

        &:hover {
          background: #d54e4a;
        }

        &.aplayer-list-light {
          background: #d54e4a;

          .aplayer-list-cur {
            display: inline-block;
          }
        }

        .aplayer-list-cur {
          display: none;
          width: 3px;
          height: 22px;
          position: absolute;
          left: 0;
          top: 5px;
          transition: background-color .3s;
        }
        .aplayer-list-index {
          color: #fff;
          margin-right: 12px;
        }
        .aplayer-list-title {
          color: white;
          flex-grow: 1;
        }
        .aplayer-list-author {
          flex-shrink: 0;
          color: #fff;
          float: right;
        }
      }
    }
  }
</style>