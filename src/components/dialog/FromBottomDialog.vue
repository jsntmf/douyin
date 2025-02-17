<template>
  <!--  <transition name="from-bottom"> -->
  <transition
      @before-enter="beforeEnter"
      @enter="enter"
      @after-enter="afterEnter"
      @before-leave="beforeLeave"
      @leave="leave"
      @after-leave="afterLeave"
      :css="false"
  >
    <div ref="dialog"
         class="FromBottomDialog"
         v-if="modelValue"
         :class="[mode,showHengGang ? '' : 'no-heng-gang']"
         :style="{'max-height':height}"
         @touchstart="start"
         @touchmove="move"
         @touchend="end"
    >
      <slot name="header"></slot>
      <div class="heng-gang" :class="mode" v-if="showHengGang">
        <div class="content"></div>
      </div>
      <slot></slot>
    </div>
  </transition>
</template>
<script>
import Dom from "../../utils/dom";

export default {
  name: "FromBottomDialog",
  props: {
    modelValue: {
      type: Boolean,
      default: false
    },
    mode: {
      type: String,
      default: 'dark'
      // default: 'light'
      // default: 'white'
    },
    maskMode: {
      type: String,
      default: 'dark'
    },
    height: {
      type: String,
      default: '70vh'
    },
    showHengGang: {
      type: Boolean,
      default: true
    },
    pageId: {
      type: String,
      default: null,
      required: true
    },
    borderRadius: {
      type: String,
      default: '.5rem .5rem 0 0'
    },
  },
  watch: {
    modelValue(newVal) {
      let page = document.getElementById(this.pageId)
      if (newVal) {
        this.pagePosition = this.$getCss2(page, 'position')
        page.style.position = 'absolute'
        this.scroll = document.documentElement.scrollTop
        document.body.style.position = 'fixed'
        document.body.style.top = -this.scroll + 'px'

        let maskTemplate = `<div class="Mask fade-in ${this.maskMode}"></div>`
        let mask = new Dom().create(maskTemplate)
        mask.on('click', () => this.hide(false))
        page.appendChild(mask.els[0])
      } else {
        let page = document.getElementById(this.pageId)
        page.style.position = this.pagePosition || 'fixed'
        document.body.style.position = 'static'
        document.documentElement.scrollTop = this.scroll

        let mask = new Dom('.Mask').replaceClass('fade-in', 'fade-out')
        setTimeout(() => {
          mask.remove()
        }, 300)
      }
    },
  },
  data() {
    return {
      scroll: 0,
      startLocationY: 0,
      moveYDistance: 0,
      startTime: 0,
      pagePosition: null
    }
  },
  computed: {},
  created() {
  },
  methods: {
    beforeEnter(el) {
      this.$setCss(el, 'transition-duration', `200ms`)
      this.$setCss(el, 'transform', `translate3d(0,${this.height},0)`)
    },
    enter(el, done) {
      setTimeout(() => {
        this.$setCss(el, 'transform', `translate3d(0,0,0)`)
      }, 0)
      setTimeout(() => {
        // this.$setCss(el, 'transition-duration', `0ms`)
        this.$setCss(el, 'transform', `none`)
        done()
      }, 200)
    },
    afterEnter() {
    },
    beforeLeave(el) {
      this.$setCss(el, 'transition-duration', `200ms`)
      this.$setCss(el, 'transform', `translate3d(0,0,0)`)
    },
    leave(el, done) {
      //ref获取不到
      let maxHeight = new Dom('.FromBottomDialog').css('max-height')
      this.$setCss(el, 'transform', `translate3d(0,${maxHeight},0)`)
      setTimeout(done, 200)
    },
    afterLeave() {
    },

    hide(val = false) {
      this.$emit('update:modelValue', val)
      this.$emit('cancel')
    },
    start(e) {
      if (this.$refs.dialog.scrollTop !== 0) return
      this.startLocationY = e.touches[0].pageY
      this.startTime = Date.now()
    },
    move(e) {
      if (this.$refs.dialog.scrollTop !== 0) return
      this.moveYDistance = e.touches[0].pageY - this.startLocationY
      if (this.moveYDistance > 0) {
        this.$setCss(this.$refs.dialog, 'transition-duration', `0ms`)
        this.$setCss(this.$refs.dialog, 'transform', `translate3d(0,${this.moveYDistance}px,0)`)
      }
    },
    end(e) {

      //点击
      if (Date.now() - this.startTime < 150 && Math.abs(this.moveYDistance) < 30) {
        return
      }

      //滑动
      if (this.$refs.dialog.scrollTop !== 0) return
      let clientHeight = this.$refs.dialog.clientHeight
      this.$setCss(this.$refs.dialog, 'transition-duration', `300ms`)
      if (Math.abs(this.moveYDistance) > clientHeight / 2) {
        this.$setCss(this.$refs.dialog, 'transform', `translate3d(0,${clientHeight}px,0)`)
        setTimeout(this.hide, 300)
      } else {
        this.$setCss(this.$refs.dialog, 'transform', `translate3d(0,0,0)`)
        setTimeout(() => {
          this.$setCss(this.$refs.dialog, 'transform', 'none')
          // this.$setCss(this.$refs.dialog, 'transition-duration', `0ms`)
        }, 300)
      }
      this.moveYDistance = 0
    }
  }
}
</script>

<style scoped lang="less">
@import "../../assets/less/index";

.FromBottomDialog {
  z-index: 9;
  position: fixed;
  width: 100%;
  overflow: auto;
  padding-top: 2.4rem;
  bottom: 0;
  left: 0;
  box-sizing: border-box;
  border-radius: v-bind(borderRadius);
  transition: all .3s;

  &.dark {
    background: @main-bg;
  }

  &.light {
    background: whitesmoke;
  }

  &.white {
    background: white;
  }

  &.no-heng-gang {
    padding-top: 0;
  }

  .heng-gang {
    border-radius: .5rem .5rem 0 0;
    z-index: 3;
    width: 100%;
    position: fixed;
    height: 2rem;
    display: flex;
    transform: translateY(-2.4rem);
    justify-content: center;
    align-items: center;

    &.dark {
      background: @main-bg;

      .content {
        background: @second-btn-color;
      }
    }

    &.light {
      background: whitesmoke;

      .content {
        background: darkgray;
      }
    }

    &.white {
      background: white;

      .content {
        background: darkgray;
      }
    }

    .content {
      border-radius: 2px;
      height: .4rem;
      width: 3rem;
    }
  }
}
</style>
