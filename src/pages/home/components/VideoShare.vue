<template>
  <from-bottom-dialog
      :page-id="pageId"
      v-model="modelValue"
      @cancel="closeShare1"
      :touch-moved="false"
      maskMode="light"
      height="60vh"
      mode="light">
    <div class="video-share">
      <div class="shares">
        <to-share item-type="weChat" :need-down="true" :can-download="canDownload"
                  @copy="copyLink"
                  @click="closeShare"/>
        <to-share item-type="weChatZone" :need-down="true" :can-download="canDownload"
                  @copy="copyLink"
                  @click="closeShare"/>
        <to-share item-type="qq" :need-down="true" :can-download="canDownload"
                  @copy="copyLink"
                  @click="closeShare"/>
        <to-share item-type="download" :need-down="true" :can-download="canDownload" @click="closeShare()"/>
        <to-share item-type="report" @click="$nav('/home/report',{mode:this.mode})"/>
        <to-share item-type="copyPassword" @click="copyLink"/>
        <to-share :item-type="isCollect?'collectYellow':'collect'" @click="toggleCollect"/>
        <to-share item-type="comeon" @click="$no"/>
        <to-share item-type="dou" @click="$no"/>
        <to-share item-type="copyLink" @click="copyLink"/>
        <template v-if="isShowMore">
          <to-share item-type="duoshan" @click="isShowMore = true"/>
          <to-share item-type="totoutiao" @click="isShowMore = true"/>
        </template>
        <to-share v-else item-type="share" @click="isShowMore = true"/>
        <to-share item-type="dislike" @click="isShowMore = true"/>
        <to-share item-type="bizhi" @click="$no"/>
        <to-share item-type="code" @click="$no"/>
      </div>
      <div class="friends">
        <div class="item" v-for="item in friends.all">
          <img class="left" v-lazy="$imgPreview(item.avatar)" alt="">
          <div class="right">
            <span>{{ item.name }}</span>
            <b-button size="small" :type="item.shared?'dark':'primary'"
                      @click="item.shared = true">
              {{ item.shared ? '已' : '' }}分享
            </b-button>
          </div>
        </div>
        <div class="more" @click="closeShare($nav('/message/share-to-friend'))">
          <img class="left" src="../../../assets/img/icon/components/video/more-dark.png">
          <span>更多朋友</span>
        </div>
      </div>
    </div>
  </from-bottom-dialog>
</template>

<script lang="jsx">
import {mapState} from "vuex";
import FromBottomDialog from "../../../components/dialog/FromBottomDialog";
import LoadingCircle from "./LoadingCircle";
// import DouyinCode from "./DouyinCode";

export default {
  name: "Share",
  components: {
    FromBottomDialog,
    LoadingCircle,
    // DouyinCode,
    'ToShare': {
      components: {
        LoadingCircle
      },
      props: {
        itemType: {
          type: String,
          default: 'weChat'
        },
        needDown: {
          type: Boolean,
          default: false
        },
        canDownload: {
          type: Boolean,
          default: true
        }
      },
      data() {
        return {
          progress: 0,
          loading: false,
          text: {
            weChat: '微信',
            weChatZone: '朋友圈',
            qq: 'QQ',
            download: '保存本地',
            report: '举报',
            copyPassword: '复制口令',
            copyLink: '复制链接',
            collect: '收藏',
            collectYellow: '取消收藏',
            comeon: '一起看',
            dou: '帮上热门',
            share: '更多分享',
            duoshan: '多闪',
            totoutiao: '今日头条',
            dislike: '不感兴趣',
            bizhi: '动态壁纸',
            code: '抖音码',
          },
        }
      },
      computed: {
        styleCanDownload() {
          if (!this.canDownload) {
            return this.itemType !== 'download';
          }
          return true
        }
      },
      methods: {
        displayText() {
          if (this.loading) {
            return this.progress !== 100 ? '下载中' : this.text[this.itemType]
          }
          return this.text[this.itemType]
        },
        click(e) {
          if (!this.canDownload) {
            if (this.itemType === 'download') {
              this.$stopPropagation(e)
            } else {
              this.$notice('作者已关闭下载功能')
              this.$emit('copy')
            }
            return
          }
          if (this.needDown) this.$stopPropagation(e)
          else return
          if (this.progress === 100) {
            this.$notice('未实现分享跳转到其他App')
          } else {
            this.loading = true
            let interval = setInterval(() => {
              if (this.progress < 100) {
                this.progress++
              } else {
                clearInterval(interval)
                this.loading = false
                this.$emit('click')
                this.$emit('click')
                this.$notice('未实现分享跳转到其他App')
              }
            }, 10)
          }
        }
      },
      render() {
        return (
            <div className="to" onClick={this.click} style={{opacity: this.styleCanDownload ? '1' : '0.5'}}>
              <div className="wrapper">
                {this.loading ?
                    <div className="loading-wrapper" style="width: 80%;height: 80%;">
                      <LoadingCircle v-model={this.progress}/>
                    </div>
                    :
                    <img src={`/src/assets/img/icon/components/share/${this.itemType}.png`} alt=""/>
                }
              </div>
              <span>{this.displayText()}</span>
            </div>
        )
      }
    }
  },
  props: {
    modelValue: false,
    videoId: {
      type: String,
      default: null
    },
    pageId: {
      type: String,
      default: 'home-index'
    },
    canDownload: {
      type: Boolean,
      default: false
    },
  },
  computed: {
    ...mapState(['friends']),
  },
  watch: {
    modelValue(newVal) {
      if (!newVal) {
        this.loading = {
          weChat: false,
          weChatZone: false,
        }
        this.progress = 0
        this.isShowMore = false
      }
    }
  },
  data() {
    return {
      isCollect: false,
      isShowMore: false,
      loading: {
        weChat: false,
        weChatZone: false,
      },
      text: {
        weChat: '微信',
        weChatZone: '朋友圈'
      },
      progress: 0
    }
  },
  methods: {
    displayText(type) {
      if (this.loading[type]) {
        return this.progress !== 100 ? '下载中' : this.text[type]
      }
      return this.text[type]
    },
    async copyLink() {
      this.closeShare()
      this.$showLoading()
      await this.$sleep(500)
      this.$hideLoading()
      this.$notice('复制成功')
    },
    toggleCollect() {
      this.closeShare()
      if (this.isCollect) {
        this.$notice('取消收藏成功')
      } else {
        this.$notice('收藏成功')
      }
      this.isCollect = !this.isCollect
    },
    toggleCall(item) {
      item.select = !item.select
    },
    closeShare() {
      this.$emit("update:modelValue", false)
    },
    closeShare1() {
      this.$emit("update:modelValue", false)
    }
  }
}
</script>

<style lang="less" scoped>
@import "@/assets/less/index";

.video-share {
  height: 60vh;
  width: 100%;
  border-radius: 1rem 1rem 0 0;
  color: white;
  box-sizing: border-box;

  .friends {
    border-radius: 1rem;
    margin: 1rem;
    padding: 0 @padding-page;
    @avatar-width: 3.8rem;
    font-size: 1.4rem;
    background: white;

    @item-width: 5.5rem;

    .item {
      height: @item-width;
      color: black;
      display: flex;
      align-items: center;
      border-bottom: 1px solid #f3f3f3;

      .left {
        width: @avatar-width;
        height: @avatar-width;
        border-radius: 50%;
        margin-right: 1.5rem;
      }

      .right {
        font-size: 1.4rem;
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: space-between;

        img {
          height: 2rem;
        }
      }
    }

    .more {
      height: @item-width;
      color: black;
      background: white;
      display: flex;
      align-items: center;
      border-bottom: 1px solid #f3f3f3;

      .left {
        width: 2rem;
        height: 2rem;
        border-radius: 50%;
        margin-left: 1rem;
        margin-right: 2rem;
      }
    }
  }

  @icon-width: 5.8rem;

  :deep(.shares) {
    @space-width: 1.5rem;

    overflow-x: scroll;
    display: flex;
    padding-right: @space-width * 2;

    .to {
      margin-left: @space-width;
      margin-bottom: @space-width;

      .wrapper {
        box-sizing: border-box;
        background: white;
        width: @icon-width;
        height: @icon-width;
        padding: 1.2rem;
        border-radius: 1.5rem;
        display: flex;
        align-items: center;
        justify-content: center;

        img {
          width: 100%;
          //height: 100%;
        }
      }

      span {
        margin-top: .7rem;
        color: black;
        font-size: 1rem;
        display: block;
        text-align: center;
      }
    }
  }


  .loading {
    width: 60%;
    height: 60%;
    position: relative;

    @border-width: 3.1px;

    .quan1 {
      position: absolute;
      box-sizing: border-box;
      width: 100%;
      height: 100%;
      border: @border-width solid darkgrey;
      border-radius: 50%;
    }

    .quan2 {
      position: absolute;
      box-sizing: border-box;
      width: 100%;
      height: 100%;
      border: @border-width solid #514F56;
      border-radius: 50%;
    }

  }


}

</style>
