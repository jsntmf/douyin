<template>
  <div class="FaceToFace">
    <BaseHeader style="background: black">
      <template v-slot:right>
        <span class="f16" @click="$nav('/common-setting')">设置</span>
      </template>
    </BaseHeader>
    <div class="content">
    </div>
  </div>
</template>
<script>
//TODO 动画效果麻烦
export default {
  name: "FaceToFace",
  data() {
    return {}
  },
  computed: {},
  created() {
  },
  methods: {}
}
</script>

<style scoped lang="less">
@import "../../assets/less/index";

.FaceToFace {
  background: black;
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  overflow: auto;
  color: white;

  .content {
    padding-top: 6rem;
  }
}
</style>
