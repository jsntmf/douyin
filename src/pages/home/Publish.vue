<template>
  <div class="Publish">
    <video id="video" autoplay="autoplay" style="width: 100%;height:calc(100% - 6rem);"></video>
    <div class="footer">
      <SlideRowList v-model:active-index="activeIndex">
        <SlideItem style="min-width: 20vw;min-height: 6rem;"></SlideItem>
        <SlideItem style="min-width: 20vw;min-height: 6rem;"></SlideItem>
        <SlideItem style="min-width: 25vw;min-height: 6rem;" @click="activeIndex = 0">
          <span :class="activeIndex + 2 === 2?'active':''">分段拍</span>
        </SlideItem>
        <SlideItem style="min-width: 20vw;min-height: 6rem;" @click="activeIndex = 1">
          <span :class="activeIndex + 2 === 3?'active':''">快拍</span>
        </SlideItem>
        <SlideItem style="min-width: 20vw;min-height: 6rem;" @click="activeIndex = 2">
          <span :class="activeIndex + 2 === 4?'active':''">影集</span>
        </SlideItem>
        <SlideItem style="min-width: 20vw;min-height: 6rem;" @click="activeIndex = 3">
          <span :class="activeIndex + 2 === 5?'active':''">开直播</span>
        </SlideItem>
      </SlideRowList>
    </div>
    <div class="float">
      <img class="close" src="../../assets/img/icon/close-white.png" alt="" @click="$back">
      <div class="choose-music">
        <img src="../../assets/img/icon/close-white.png" alt="">
        <span>选择音乐</span>
      </div>
      <div class="toolbar">
        <div class="tool" @click.stop="$emit('showComments')">
          <img src="../../assets/img/icon/close-white.png" alt="">
          <span>翻转</span>
        </div>
        <div class="tool" @click.stop="$emit('showComments')">
          <img src="../../assets/img/icon/close-white.png" alt="">
          <span>快慢速</span>
        </div>
        <div class="tool" @click.stop="$emit('showComments')">
          <img src="../../assets/img/icon/close-white.png" alt="">
          <span>滤镜</span>
        </div>
        <div class="tool" @click.stop="$emit('showComments')">
          <img src="../../assets/img/icon/close-white.png" alt="">
          <span>美化</span>
        </div>
        <div class="tool" @click.stop="$emit('showComments')">
          <img src="../../assets/img/icon/close-white.png" alt="">
          <span>倒计时</span>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import {mapState} from "vuex";

export default {
  name: "Publish",
  data() {
    return {
      video: null,
      activeIndex: 1
    }
  },
  computed: {
    ...mapState(['bodyHeight', 'bodyWidth'])
  },
  mounted() {
    //获得video摄像头区域
    this.video = document.getElementById("video");
    this.getMedia()
  },
  methods: {
    getMedia() {
      let constraints = {video: {width: this.bodyWidth, height: this.bodyHeight - 60}, audio: false};
      let promise = navigator.mediaDevices.getUserMedia(constraints);
      promise.then((MediaStream) => {
        this.video.srcObject = MediaStream;
        this.video.play();
      }).catch(function (PermissionDeniedError) {
        console.log(PermissionDeniedError);
      })
    },
  },
}
</script>

<style scoped lang="less">
@import "../../assets/less/index";

.Publish {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;
  background: black;

  .footer {
    font-size: 1.5rem;
    font-weight: bold;
    color: @second-text-color;

    .base-slide-item {
      display: flex;
      align-items: center;
    }

    .active {
      color: white;
    }
  }

  .float {
    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    height: calc(100% - 60px);

    .close {
      position: absolute;
      left: 2rem;
      top: 2rem;
      width: 2rem;
      height: 2rem;
    }

    .choose-music {
      position: absolute;
      left: 50%;
      top: 2rem;
      transform: translateX(-50%);
      border-radius: 2rem;
      background: #333333;
      padding: .5rem 1.5rem;
      display: flex;
      align-items: center;

      img {
        margin-right: .5rem;
        width: 1.2rem;
        height: 1.2rem;
      }
    }

    .toolbar {
      position: absolute;
      top: 2rem;
      right: 1rem;

      .tool {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-bottom: 2rem;
        font-size: 1rem;

        img {
          width: 2rem;
          height: 2rem;
          margin-bottom: .5rem;
        }
      }
    }
  }


}
</style>
