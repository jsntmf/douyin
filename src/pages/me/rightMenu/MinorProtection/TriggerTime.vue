<template>
  <div class="TriggerTime">
    <BaseHeader>
      <template v-slot:center>
        <span class="f16">触发时间</span>
      </template>
    </BaseHeader>
    <div class="content">
      <div class="row" @click="setTriggerTime(enums.TRIGGER_TIME.TIME40)">
        <div class="left">40分钟</div>
        <div class="right" v-if="triggerTime === enums.TRIGGER_TIME.TIME40">
          <img src="../../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
      <div class="row" @click="setTriggerTime(enums.TRIGGER_TIME.TIME60)">
        <div class="left">60分钟</div>
        <div class="right" v-if="triggerTime === enums.TRIGGER_TIME.TIME60">
          <img src="../../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
      <div class="row" @click="setTriggerTime(enums.TRIGGER_TIME.TIME90)">
        <div class="left">90分钟</div>
        <div class="right" v-if="triggerTime === enums.TRIGGER_TIME.TIME90">
          <img src="../../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
      <div class="row" @click="setTriggerTime(enums.TRIGGER_TIME.TIME120)">
        <div class="left">120分钟</div>
        <div class="right" v-if="triggerTime === enums.TRIGGER_TIME.TIME120">
          <img src="../../../../assets/img/icon/ok-red.png" alt="">
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import enums from "../../../../utils/enums";

export default {
  name: "TriggerTime",
  data() {
    return {
      enums,
      triggerTime: enums.TRIGGER_TIME.TIME60
    }
  },
  created() {
    this.triggerTime = ~~this.$route.query.triggerTime
  },
  methods: {
    setTriggerTime(type) {
      this.triggerTime = type
      localStorage.setItem('changeTriggerTime', type)
    }
  }
}
</script>

<style scoped lang="less">
@import "../../../../assets/less/index";

.TriggerTime {
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
