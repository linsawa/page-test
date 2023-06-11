<template>
  <div class="studentItem" :class="{selected: isSelected}" @click="studentItemClick">
    <el-checkbox v-model="resumeItem.ischecked" />
    <div class="info">
      <div class="stu-info">
        <div class="stu-info-l">
          <div class="avater">
            <img :src="resumeItem.headImg" alt="">
          </div>
          <div class="desc">
            <div class="name">{{ resumeItem.realName }}</div>
            <div class="major">
              <span>{{ resumeItem.graduateDate || '暂未填写' }}</span>
              &ensp;|&ensp;
              <span>{{ resumeItem.majorName || '暂未填写' }}</span>
            </div>
          </div>
        </div>
        <div class="stu-info-r">
          <el-rate
            v-model="resumeItem.score"
            disabled
            :max="3"
            :colors="['#30B0BF', '#30B0BF', '#30B0BF']"
            score-template="{value}"
          />
        </div>
      </div>
      <div class="desc">
        <div class="target">
          求职意向: <span>{{ resumeItem.expectPosition || "暂未填写" }}</span> |
          <span>{{ resumeItem.expCity || "暂未填写" }}</span> |
          <span v-if="resumeItem.expectSalary"><span>{{ resumeItem.expectSalary }}</span>元/月</span>
          <span v-else>暂未填写</span>
        </div>
        <div class="forte">个人优势：{{ resumeItem.evaluationSelf || '暂未填写' }}</div>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  components: {},
  props: {
    resumeItem: {
      type: Object,
      default: () => {}
    },
    selectedUid: {
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
      return this.selectedUid === this.resumeItem.uid
    }
  },
  methods: {
    studentItemClick() {
      this.$emit('itemClick', this.resumeItem)
    }
  }
}
</script>

<style scoped lang="scss">
.studentItem.selected {
  background: #EAF7F9;
}
.studentItem {
  display: flex;
  align-items: center;
  box-sizing: border-box;
  // background-color: #ccc;
  padding: 0 16px;
  // width: 390px;
  width: 100%;
  height: 158px;
  display: flex;
  // align-items: center;
  &:hover {
    background: #EAF7F9;
  }
  .info {
    margin-left: 17px;
    width: 100%;
    .stu-info {
      display: flex;
      justify-content: space-between;
      margin-bottom: 16px;
      .stu-info-l {
        display: flex;
        .avater {
          height: 40px;
          width: 40px;
          border-radius: 50%;
          overflow: hidden;
          margin-right: 8px;
          img {
            width: 100%;
            height: 100%;
          }
        }
        .desc {
          .name {
            font-size: 16px;
            font-weight: 500;
            color: #333333;
            margin-bottom: 4px;
          }
          .major {
            font-size: 14px;
            font-weight: 400;
            color: #999999;
          }
        }
      }
      .stu-info-r {
        font-size: 14px;
      }
    }
    .desc {
      // width: 308px;
      font-size: 14px;
      color: #999;
      .target {
        & span:nth-child(1) {
          color: #333333;
        }
        & span:nth-child(2) {
          color: #333333;
        }
        & span:nth-child(3) {
          color: #30B0BF;
          font-weight: bold;
        }
      }
      .forte {
        display: -webkit-box;
        -webkit-box-orient: vertical;
        -webkit-line-clamp: 1;
        overflow: hidden;
        margin-top: 8px;
      }
    }
  }
}
</style>
