<template>
  <div id="AllMessage">
    <BaseHeader>
      <template v-slot:center>
        <div class="center" @click="isShowType = !isShowType">
          <span class="f16">{{ showTypeText }}</span>
          <img :class="{show:isShowType}" src="../../assets/img/icon/arrow-up-white.png" alt="">
        </div>
      </template>
    </BaseHeader>
    <transition name="fade">
      <div class="type-dialog" v-if="isShowType">
        <div class="dialog-content">
          <div class="row" @click="toggleShowType(1)">
            <div class="left">
              <img src="../../assets/img/icon/message/done-gray.png" alt="">
              <span>全部消息</span>
            </div>
          </div>
          <div class="row" @click="toggleShowType(2)">
            <div class="left">
              <img src="../../assets/img/icon/message/like-gray.png" alt="">
              <span>赞</span>
            </div>
          </div>
          <div class="row" @click="toggleShowType(3)">
            <div class="left">
              <img src="../../assets/img/icon/message/call-gray.png" alt="">
              <span>@我的</span>
            </div>
          </div>
          <div class="row" @click="toggleShowType(4)">
            <div class="left">
              <img src="../../assets/img/icon/message/comment-gray.png" alt="">
              <span>评论</span>
            </div>
          </div>
        </div>
        <div class="mask" @click="isShowType = false"></div>
      </div>
    </transition>
    <div class="content">
      <Loading v-if="loading"/>
      <Scroll
          v-else
          ref="mainScroll"
          :use-refresh="true"
          @refresh="refresh"
          @pulldown="loadData">
        <div class="messages">
          <div class="message" @click="$nav('/message/visitors')">
            <div class="left">
              <img v-lazy="$imgPreview(userinfo.avatar)" alt="" class="avatar">
            </div>
            <div class="right">
              <div class="desc">
                <div class="top">
                  <div class="name">主页访客</div>
                </div>
                <div class="desc-content">
                  4人最近访问了你的主页，看看他们是谁
                </div>
                <div class="bottom">
                  <div class="time">01-11</div>
                </div>
              </div>
              <img v-lazy="$imgPreview(userinfo.avatar)" alt="" class="poster">
            </div>
          </div>
          <div class="message" v-for="item in showMessageList" @click="$no">
            <div class="left">
              <img v-lazy="$imgPreview(item.author.avatar)" alt="" class="avatar">
              <img v-if="selectShowType === 2" src="../../assets/img/icon/message/love-message.webp" alt=""
                   class="type">
              <img v-if="selectShowType === 3" src="../../assets/img/icon/message/call-message.webp" alt=""
                   class="type">
              <img v-if="selectShowType === 4" src="../../assets/img/icon/message/comment-message.webp" alt=""
                   class="type">
            </div>
            <div class="right">
              <div class="desc">
                <div class="top">
                  <div class="name">{{ item.author.nickname }}</div>
                  <div class="tag">朋友</div>
                </div>
                <div class="desc-content">
                  <span v-if="selectShowType === 1">好好看啊</span>
                  <span v-if="selectShowType === 2">赞了你的作品</span>
                  <span v-if="selectShowType === 3">@{{ userinfo.nickname }}</span>
                  <span v-if="selectShowType === 4">好好看啊</span>
                </div>
                <div class="bottom">
                  <div class="type" v-if="selectShowType === 3">在评论中提到了你</div>
                  <div class="type" v-if="selectShowType === 4">回复了你的评论</div>
                  <div class="time">01-11</div>
                </div>
              </div>
              <img v-lazy="$imgPreview(item.video+'?vframe/jpg/offset/0/w/300')" alt="" class="poster">
            </div>
          </div>
          <div class="look-all" v-if="!showAll" @click="showAll = true">
            <span>查看全部</span>
            <back/>
          </div>
        </div>
        <div class="title">
          <span>朋友推荐</span>
          <img src="../../assets/img/icon/about-gray.png" alt="">
        </div>
        <Peoples v-model:list="recommend"
                 :loading="loadingMore"
                 mode="recommend"/>
        <Loading :is-full-screen="false" v-if="loadingMore"/>
      </Scroll>
    </div>
  </div>
</template>
<script>
import {mapState} from "vuex";
import People from "../people/components/People";
import Scroll from "../../components/Scroll";
import Loading from "../../components/Loading";
import Peoples from "../people/components/Peoples";
import resource from "../../assets/data/resource.js";
import BasePage from "../BasePage";


export default {
  extends: BasePage,
  name: "AllMessage",
  components: {
    Scroll,
    People,
    Loading,
    Peoples
  },
  data() {
    return {
      loading: false,
      loadingMore: false,
      isShowType: false,
      showAll: false,
      recommend: [],
      messages: [],
      selectShowType: 1
    }
  },
  computed: {
    ...mapState(['userinfo', 'friends']),
    showTypeText() {
      switch (this.selectShowType) {
        case 1:
          return '全部消息'
        case 2:
          return '赞'
        case 3:
          return '@我的'
        case 4:
          return '评论'
      }
    },
    showMessageList() {
      if (this.showAll) {
        return this.messages
      }
      return this.messages.slice(0, 2)
    }
  },
  created() {
    this.getData()
  },
  methods: {
    async getData() {
      this.loading = true
      await this.$sleep(800)
      this.loading = false
      this.recommend = this.$clone(this.friends.all)
      this.fans = this.$clone(this.friends.all)
      this.recommend.map(v => {
        v.type = -1
      })
      this.messages = this.$clone(resource.videos)
    },
    toggleShowType(index) {
      this.selectShowType = index
      this.isShowType = false
    },
    remove(index) {
      this.$notice('将不会再为你推荐该用户')
      this.recommend.splice(index, 1)
    },
    async refresh() {
      await this.$sleep(1000)
      this.$refs.mainScroll.refreshEnd()
    },
    async loadData() {
      if (this.loadingMore) return
      this.loadingMore = true
      await this.$sleep(500)
      this.loadingMore = false
      let temp = this.$clone(this.friends.all)
      temp.map(v => {
        v.type = -1
      })
      this.recommend = this.recommend.concat(temp)
    }
  }
}
</script>

<style scoped lang="less">
@import "@/assets/less/index";


.list-complete-enter-from,
.list-complete-leave-to {
  opacity: 0;
}

.list-complete-leave-active {
  position: absolute;
}

#AllMessage {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;
  font-size: 1.4rem;

  .center {
    display: flex;
    align-items: center;

    img {
      width: 1.5rem;
      transform: rotate(180deg);
      transition: all .3s;
    }

    .show {
      transform: rotate(0deg);
    }
  }

  .type-dialog {
    z-index: 9;
    position: fixed;
    height: 100vh;
    width: 100vw;
    margin-top: @header-height;

    .dialog-content {
      background: @main-bg;

      img {
        width: 1.6rem;
      }
    }

    .mask {
      z-index: 8;
      position: fixed;
      height: 100vh;
      width: 100vw;
      background: @mask-dark;
    }
  }

  .content {
    padding: @padding-page;
    padding-top: @header-height;

    .scroll {
      height: calc(100vh - @header-height);
    }

    .messages {
      .message {
        margin-bottom: 2rem;
        display: flex;

        &:first-child {
          margin-top: 2rem;
        }

        .left {
          display: flex;
          align-items: center;
          position: relative;

          .avatar {
            width: 4.8rem;
            border-radius: 50%;
          }

          .type {
            position: absolute;
            right: 0;
            bottom: 1rem;
            width: 1.7rem;
            padding: .25rem;
            border-radius: 50%;
            background: black;
          }

          margin-right: 1rem;
        }

        .right {
          flex: 1;
          display: flex;
          justify-content: space-between;

          .desc {
            display: flex;
            flex-direction: column;
            justify-content: space-between;

            .top {
              //margin-bottom: 1rem;
              display: flex;
              align-items: center;

              .tag {
                color: @second-text-color;
                padding: .1rem .4rem;
                margin-left: 1rem;
                border-radius: .2rem;
                background: @second-btn-color-tran;
                font-size: 1rem;
              }
            }

            .bottom {
              //margin-top: 1rem;
              display: flex;
              align-items: center;
              font-size: 1.2rem;
              color: @second-text-color;

              .type {
                margin-right: 1rem;
              }
            }
          }

          .poster {
            margin-left: 1rem;
            width: 6.4rem;
            height: 6.4rem;
            object-fit: cover;
            border-radius: .3rem;
          }
        }
      }

      .look-all {
        font-size: 1.2rem;
        color: @second-text-color;
        display: flex;
        justify-content: center;
        align-items: center;

        .close {
          margin-left: 1rem;
          transform: rotate(270deg) !important;
          width: 1.2rem;
          height: 1.2rem;
        }
      }
    }


    .title {
      margin-top: 1.5rem;
      margin-bottom: 1rem;
      font-size: 1.2rem;
      display: flex;
      align-items: center;

      img {
        margin-left: .5rem;
        width: 1.3rem;
      }
    }
  }
}
</style>
