<template>
  <div class="positionItem" :class="{selected: isSelected}" @click="positionItemClick">
    <div class="warp">
      <div class="keypoint">
        <div>{{ info.name }}</div>
        <div v-if="info.minSalary"><span>{{ info.minSalary }}</span>元/月</div>
        <div v-else>面议</div>
      </div>
      <div class="info">
        <span>{{ info.workingCity }}</span> |
        <span>{{ info.degree || '学历不限' }}</span> |
        <span>{{ info.orgName }}</span>
      </div>
      <div class="count">
        <div>招<span>{{ info.numbers || 1 }}</span>人</div>
        <div>待审核：<span>{{ info.resumeReceivedGroupCount[1] || 0 }}</span>人</div>
        <div>待面试：<span>{{ info.resumeReceivedGroupCount[2] || 0 }}</span>人</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    info: {
      type: Object,
      default: () => {}
    },
    selectedPid: {
      type: String,
      default: ''
    }
  },
  data() {
    return {

    }
  },
  computed: {
    isSelected() {
      return this.selectedPid === this.info.pid
    }
  },
  methods: {
    positionItemClick() {
      this.$emit('itemClick', this.info)
    }
  }
}
</script>

<style scoped lang="scss">
.positionItem.selected {
  background: #EAF7F9;
}

.positionItem {
  .warp {
    &:hover {
      background-color: #EAF7F9;
    }
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 114px;
    // background: #FFFFFF;
    border-radius: 4px;
    border: 1px solid #F4F4F4;
    padding: 16px;
    .keypoint {
      display: flex;
      justify-content: space-between;
      color: #999;
      & div:nth-child(1) {
        font-weight: 500;
        color: #333333;
        font-size: 16px;
      }
      & div:nth-child(2) span {
        color: #30B0BF;
        font-weight: 600;
      }
    }
    .info {
      font-size: 14px;
      margin-top: 4px;
      margin-bottom: 8px;
      font-weight: 400;
      color: #999999;
    }
    .count {
      display: flex;
      justify-content: space-between;
      font-size: 14px;
      color: #999999;
      font-weight: 400;
      padding-top: 8px;
      border-top: 1px solid #F4F4F4;
      & div:nth-child(2) span {
        color: #707070;
        font-weight: 600;
      }
      & div:nth-child(3) span {
        color: #FF5F5F ;
        font-weight: 600;
      }
    }
  }
}
</style>
