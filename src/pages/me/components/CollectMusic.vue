<template>
  <div id="CollectMusic">
    <SlideColumnList
        :changeActiveIndexUseAnim="false"
        v-model:active-index="activeIndex"
        :canMove="slideCanMove">
      <SlideItemMusic
          :ref="setItemRef"
          @showList="isShowList = true"
          @showShare="isSharing = true"
          @previous="previous"
          @next="next"
          @slideCanMove="e => this.slideCanMove = e"
          v-model="list[index]"
          v-model:isLoop="isLoop"
          v-for="(item,index) in list "/>
    </SlideColumnList>
    <from-bottom-dialog
        mask-mode="lightgray"
        page-id="CollectMusic"
        border-radius="1.5rem 1.5rem 0 0"
        :show-heng-gang="false"
        height="70vh"
        v-model="isShowList">
      <div class="music-list-dialog">
        <div class="music-list-header">
          <div class="left">待播清单</div>
          <div class="right" @click="isLoop = !isLoop">
            <img v-show="isLoop" src="@/assets/img/icon/me/loop.png" alt="">
            <img v-show="!isLoop" src="@/assets/img/icon/me/play-normal.png" alt="">
            <span>{{ isLoop ? '单曲循环' : '顺序播放' }}</span>
          </div>
        </div>
        <div class="wrapper">
          <div class="l-row"
               @click="play(index)"
               :class="{active:activeIndex === index}"
               v-for="(item,index) in list">
            <div class="left">
              <img v-if="activeIndex === index" src="@/assets/img/icon/me/pinlv.gif" alt="" class="play-icon">
              <div class="name">{{ item.name }}</div>
              <div class="author">{{ item.author }}</div>
            </div>
            <back class="right" mode="gray" img="close"/>
          </div>
        </div>
        <div class="footer" @click="isShowList = false">取消</div>
      </div>
    </from-bottom-dialog>

    <Share v-model="isSharing"
           mode="my-music"
           ref="share"
           pageId="GuessMusic"
           @ShareToFriend="delayShowDialog( e => this.isShowShareToFriend = true)"
    />

    <ShareToFriend pageId="GuessMusic" v-model="isShowShareToFriend"/>
  </div>
</template>
<script>
import FromBottomDialog from "../../../components/dialog/FromBottomDialog";
import Switches from "../../message/components/swtich/switches";
import SlideItemMusic from "./SlideItemMusic";
import IndicatorLight from "../../../components/slide/IndicatorLight";
import SlideColumnList from "../../../components/slide/SlideColumnList";
import Share from "../../../components/Share";
import ShareToFriend from "../../home/components/ShareToFriend";

export default {
  name: "GuessMusic",
  components: {
    FromBottomDialog,
    Switches,
    SlideItemMusic,
    IndicatorLight,
    SlideColumnList,
    Share,
    ShareToFriend
  },
  props: {
    list: {
      type: Array,
      default: []
    },
    page2SlideIndex: {
      type: Number,
      default: 0
    },
  },
  data() {
    return {
      slideCanMove: true,
      isShowShareToFriend: false,
      isShowList: false,
      isSharing: false,
      isLoop: false,
      collectSlideIndex: 0,
      isShowCollectDialog: false,
      itemRefs: []
    }
  },
  computed: {
    activeIndex: {
      get() {
        return this.page2SlideIndex
      },
      set(val) {
        this.$emit('update:page2SlideIndex', val)
      }
    }
  },
  created() {
  },
  watch: {
    activeIndex(newVal, oldVal) {
      this.itemRefs.map(ref => {
        ref.togglePlay(false)
      })
      this.itemRefs[newVal].togglePlay(true, true)
    }
  },
  methods: {
    previous() {
      if (this.activeIndex > 0) {
        this.play(this.activeIndex - 1)
      }
    },
    next() {
      if (this.activeIndex < this.list.length - 1) {
        this.play(this.activeIndex + 1)
      }
    },
    delayShowDialog(cb) {
      setTimeout(() => {
        cb()
      }, 100)
    },
    setItemRef(el) {
      if (el) {
        this.itemRefs.push(el)
      }
    },
    play(index) {
      this.activeIndex = index
      this.itemRefs.map(ref => {
        ref.togglePlay(false)
      })
      this.itemRefs[index].togglePlay(true, true)
    },
    pause() {
      this.itemRefs.map(ref => {
        ref.togglePlay(false)
      })
    }
  }
}
</script>
<style scoped lang="less">
@import "@/assets/less/index";

#CollectMusic {
  //width: 100vw;
  //height: 100vh;
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;
  font-size: 1.4rem;
}

.music-list-dialog {
  height: 70vh;
  @bg-color: #1e1d1d;
  background: @bg-color;

  .music-list-header {
    position: fixed;
    width: 100vw;
    background: @bg-color;
    box-sizing: border-box;
    border-bottom: 1px solid #2a2828;
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 5rem;
    padding: 0 @padding-page;
    border-radius: 1.5rem 1.5rem 0 0;
    z-index: 9;

    .left {
      font-size: 1.6rem;
    }

    .right {
      display: flex;
      align-items: center;
      font-size: 1.2rem;

      img {
        width: 2rem;
        margin-right: .5rem;
      }
    }
  }

  .wrapper {
    padding-top: 5rem;
  }

  .l-row {
    background: @bg-color;
    height: 5rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 @padding-page;

    &.active {
      color: @primary-btn-color;
    }

    .left {
      display: flex;
      align-items: center;

      .author {
        font-size: 1.2rem;
        color: @second-text-color;
        margin-left: 2rem;
        position: relative;
        display: flex;
        align-items: center;

        &:after {
          content: '';
          width: .6rem;
          height: .5px;
          background: @second-text-color;
          position: absolute;
          left: -1.2rem;
        }
      }

      .play-icon {
        width: 1.5rem;
        margin-right: 1rem;
      }
    }

    .right {
      width: 1.5rem;
    }
  }

  .footer {
    background: @bg-color;
    border-top: 1px solid #2a2828;
    height: 6rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}

</style>
