<template>
  <div class="DisplayType">
    <BaseHeader>
      <template v-slot:center>
        <span class="f16">展示范围</span>
      </template>
    </BaseHeader>
    <div class="content">
      <div class="row" @click="setDisplayType(enums.DISPLAY_TYPE.ALL)">
        <div class="left">公开可见</div>
        <div class="right" v-if="displayType === enums.DISPLAY_TYPE.ALL">
          <img src="../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
      <div class="row" @click="setDisplayType(enums.DISPLAY_TYPE.SCHOOL)">
        <div class="left">校友可见</div>
        <div class="right" v-if="displayType === enums.DISPLAY_TYPE.SCHOOL">
          <img src="../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
      <div class="row" @click="setDisplayType(enums.DISPLAY_TYPE.ME)">
        <div class="left">仅自己可见</div>
        <div class="right" v-if="displayType === enums.DISPLAY_TYPE.ME">
          <img src="../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import enums from "../../../utils/enums";

export default {
  name: "DisplayType",
  data() {
    return {
      enums,
      displayType: enums.DISPLAY_TYPE.ALL
    }
  },
  created() {
    this.displayType = ~~this.$route.query.displayType
  },
  methods: {
    setDisplayType(type) {
      this.displayType = type
      localStorage.setItem('changeDisplayType', type)
      this.$back()
    }
  }
}
</script>

<style scoped lang="less">
@import "../../../assets/less/index";

.DisplayType {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  color: white;

  .content {
    padding-top: 6rem;
  }

}
</style>
