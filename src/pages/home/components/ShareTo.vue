<template>
  <from-bottom-dialog
      page-id="home-index"
      v-model="showShareDialog"
      @cancel="cancel"
      maskMode="light"
      height="50vh"
      mode="white">
    <div class="option-dialog">
      <div class="buttons">
        <b-button v-if="downloading"
                  class="mb1r"
                  :border="false"
                  :progress="progress">
          <img src="../../../assets/img/icon/components/video/download-gray.png" alt="">
          <span class="second-text-color">下载中 9.2MB/{{ progress }}%</span>
        </b-button>

        <template v-if="canDownload">
          <b-button type="green" v-if="showShare2WeChatZone" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/wechatzone-white.webp" alt="">
            </template>
            发送视频到朋友圈
          </b-button>
          <b-button type="green" v-if="showShare2WeChat" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/wechat-white.webp" alt="">
            </template>
            发送视频到微信
          </b-button>
          <b-button type="qqzone" v-if="showShare2QQZone" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/qqzone-white.png" alt="">
            </template>
            发送视频到QQ空间
          </b-button>
          <b-button type="qq" v-if="showShare2QQ" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/qq-white.webp" alt="">
            </template>
            发送视频到QQ
          </b-button>
          <b-button type="webo" v-if="showShare2Webo" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/webo-white.webp" alt="">
            </template>
            发送视频到微博
          </b-button>
          <b-button v-if="!showDownload" class="mt1r" type="white" @click="$no">复制口令发给好友</b-button>
        </template>

        <template v-else>
          <b-button type="green" v-if="showShare2WeChatZone" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/wechatzone-white.webp" alt="">
            </template>
            复制口令发给好友
          </b-button>
          <b-button type="green" v-if="showShare2WeChat" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/wechat-white.webp" alt="">
            </template>
            复制口令发给好友
          </b-button>
          <b-button type="qqzone" v-if="showShare2QQZone" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/qqzone-white.png" alt="">
            </template>
            复制口令发给好友
          </b-button>
          <b-button type="qq" v-if="showShare2QQ" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/qq-white.webp" alt="">
            </template>
            复制口令发给好友
          </b-button>
          <b-button type="webo" v-if="showShare2Webo" @click="$no">
            <template v-slot:prefix>
              <img src="../../../assets/img/icon/components/video/webo-white.webp" alt="">
            </template>
            复制口令发给好友
          </b-button>
        </template>

        <template v-if="showDownload">
          <b-button type="primary" @click="$no">
            去相册查看
            <back scale="0.7" mode="light" direction="right"></back>
          </b-button>

          <b-button class="mt1r" type="white" @click="$no">
            <img src="../../../assets/img/icon/components/video/wechat.webp" alt="">
            发送视频到微信
          </b-button>
        </template>
      </div>
      <div class="dialog-friends">
        <div class="dialog-friend" v-for="item in localFriends.all" @click="share(item)">
          <img :src="$imgPreview(item.avatar)" alt="">
          <div class="right">
            <span>{{ item.name }}</span>
            <div class="share-btn" v-if="!item.select">分享</div>
            <div class="share-btn shared" v-else>已私信</div>
          </div>
        </div>
        <div class="more" @click="cancel($nav('/message/share-to-friend'))">
          <img src="../../../assets/img/icon/components/video/more-dark.png">
          <div class="right">
            <span>更多好友</span>
          </div>
        </div>
      </div>
    </div>
  </from-bottom-dialog>
</template>
<script>
import FromBottomDialog from "../../../components/dialog/FromBottomDialog";
import {mapState} from "vuex";
/*
* 分享到各种工具
* */
export default {
  name: "ShareTo",
  components: {
    FromBottomDialog
  },
  props: {
    type: {
      type: Number,
      default: -1
    },
    videoId: {
      type: String,
      default: null
    },
    canDownload: {
      type: Boolean,
      default: true
    },
  },
  watch: {
    type(newVal, oldVal) {
      this.change(newVal)
    },
    showShareDialog() {
      this.localFriends = this.$clone(this.friends)
    }
  },
  data() {
    return {
      progress: 0,
      downloading: false,

      showShareDialog: false,
      showDownload: false,
      showShare2WeChatZone: false,
      showShare2WeChat: false,
      showShare2QQZone: false,
      showShare2QQ: false,
      showShare2Webo: false,
      localFriends: [],
    }
  },
  computed: {
    ...mapState(['friends']),
  },
  created() {
  },
  methods: {
    async change(newVal) {
      if (newVal === -1) return
      this.showShareDialog = true
      if (this.canDownload) {
        let downloadedVideo = this.$storageGet('downloadedVideo', [])
        if (!downloadedVideo.find(v => v === this.videoId) && !this.downloading) {
          await this.downloadVideo()
        }
      }
      switch (newVal) {
        case 2:
          return this.showShare2WeChatZone = true
        case 3:
          return this.showShare2WeChat = true
        case 4:
          return this.showShare2QQZone = true
        case 5:
          return this.showShare2QQ = true
        case 8:
          return this.showShare2Webo = true
        case 9:
          return this.showDownload = true
      }
    },
    cancel() {
      this.showShareDialog = false
      this.showDownload = false
      this.showShare2WeChatZone = false
      this.showShare2WeChat = false
      this.showShare2QQZone = false
      this.showShare2QQ = false
      this.showShare2Webo = false
      this.$emit('update:type', -1)
    },
    downloadVideo() {
      return new Promise((resolve, reject) => {
        this.progress = 0
        this.downloading = true
        let time = setInterval(() => {
          if (this.progress >= 100) {
            let downloadedVideo = this.$storageGet('downloadedVideo', [])
            downloadedVideo.push(this.videoId)
            this.$storageSet('downloadedVideo', downloadedVideo)
            clearInterval(time)
            this.downloading = false
            resolve()
          } else {
            this.progress++
          }
        }, 5)
      })
    },
    share(item) {
      if (item.select) {
        this.$notice('已分享给朋友')
      }
      item.select = true
    }
  }
}
</script>

<style scoped lang="less">
@import "../../../assets/less/index";

.option-dialog {
  .buttons {
    padding: 0 1.5rem;

    img {
      height: 2.2rem;
      margin-right: .5rem;
    }
  }

  .dialog-friends {
    color: black;
    margin: 1rem 1rem 0 1rem;
    width: calc(100% - 2rem);
    background: white;
    border-radius: 6px 6px 0 0;

    > .dialog-friend {
      box-sizing: border-box;
      padding: .8rem;
      width: 100%;
      display: flex;
      align-items: center;
      border-bottom: 1px solid #efefef;

      img {
        border-radius: 50%;
        width: 3.8rem;
        height: 3.8rem;
      }

      .right {
        margin: 0 0.5rem 0 1.5rem;
        font-size: 1.4rem;
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: space-between;

        .share-btn {
          font-size: 1.3rem;
          color: white;
          height: 2.5rem;
          width: 6rem;
          display: flex;
          align-items: center;
          justify-content: center;
          background: @primary-btn-color;
          border-radius: 2px;

          &.shared {
            background: lightgray;
            color: @second-text-color;
          }
        }
      }
    }

    .more {
      box-sizing: border-box;
      height: 5.5rem;
      width: 100%;
      display: flex;
      align-items: center;
      border-bottom: 1px solid #efefef;
      font-size: 1.4rem;
      margin-bottom: 1.5rem;

      img {
        border-radius: 50%;
        width: 2rem;
        height: 2rem;
        margin: 0 2.2rem 0 1.5rem;
      }
    }
  }
}
</style>
