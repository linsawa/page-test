<template>
  <div class="workbenche flex space-between">
    <div class="wrap-l">
      <div class="count">
        <div class="count-item">
          <div class="count-title">校招职位数</div>
          <div>13</div>
          <div>
            比昨日&ensp;
            <span>3</span>
            <img src="" alt="">
          </div>
        </div>
        <div class="count-item">
          <div class="count-title">今日学生投递</div>
          <div>123</div>
          <div>
            比昨日&ensp;
            <span>3</span>
            <img src="" alt="">
          </div>
        </div>
        <div class="count-item">
          <div class="count-title">候选人</div>
          <div>23</div>
          <div>
            比昨日&ensp;
            <span>3</span>
            <img src="" alt="">
          </div>
        </div>
        <div class="count-item">
          <div class="count-title">未评价</div>
          <div class="warning">1</div>
          <div>
            比昨日&ensp;
            <span>3</span>
            <img src="" alt="">
          </div>
        </div>
        <div class="count-item">
          <div class="count-title">待面试</div>
          <div>3</div>
          <div>
            比昨日&ensp;
            <span>3</span>
            <img src="" alt="">
          </div>
        </div>
        <div class="count-item">
          <div class="count-title">待发offer</div>
          <div>0</div>
          <div>
            比昨日&ensp;
            <span>3</span>
            <img src="" alt="">
          </div>
        </div>
      </div>
      <div class="service-list flex space-between align-center">
        <div class="service-item">
          <img src="" alt="">
          <div>入驻院校</div>
        </div>
        <div class="service-item">
          <img src="" alt="">
          <div>发布职位</div>
        </div>
        <div class="service-item">
          <img src="" alt="">
          <div>预约宣讲会&双选会</div>
        </div>
        <div class="service-item">
          <img src="" alt="">
          <div>学生招聘管理</div>
        </div>
        <div class="service-item">
          <img src="" alt="">
          <div>职位管理</div>
        </div>
      </div>
      <div class="position">
        <div class="position-filter flex space-between align-center">
          <div class="filter-l">
            <el-input
              v-model="keyWord"
              placeholder="请输入关键字"
              size="medium"
            >
              <el-button slot="append" icon="el-icon-search" @click="handleSearch" />
            </el-input>
          </div>
          <div class="filter-r">
            <div>
              <span
                v-for="item in statusList"
                :key="item.type"
                class="mainItem"
                :class="item.isActive ? 'itemActive' : ''"
                @click="changeTab(item)"
              >
                {{ item.name }}
              </span>
            </div>
          </div>
        </div>
        <div class="position-list">
          <div
            v-for="item in 5"
            :key="item"
            class="flex align-center positionContainer"
          >
            <div class="positionInfo">
              <div class="infoTop">
                <span class="it_name">PHP开发工程师</span>
                <img
                  style="margin-left: 10px"
                  src="@/assets/images/shipin.png"
                  alt=""
                >
                <span class="it_tag tag1">宣讲会</span>
                <span class="it_tag tag2">双选会</span>
              </div>
              <div class="infoCenter">U职杭州分公司</div>
              <div class="infoBottom">
                <span>上海｜本科｜应届生｜招10人 | </span>
                <span>
                  <b style="color: #30b0bf">6000</b>元/月
                </span>
              </div>
            </div>
            <div class="positionNum flex">
              <div class="flex flex-direction align-center mr-20">
                <span class="desc">待审核(人)</span>
                <span class="num">20</span>
              </div>
              <div class="flex flex-direction align-center mr-20">
                <span class="desc">待面试(人)</span>
                <span class="num">20</span>
              </div>
              <div class="flex flex-direction align-center mr-20">
                <span class="desc">待回答提问</span>
                <span class="num">20</span>
              </div>
              <div class="flex align-center position-status">
                <span class="onShell">上架中</span>
                <!-- <span class="offShell">未上架</span> -->
              </div>
            </div>
            <div class="more">
              <i class="el-icon-more" />
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="wrap-r">
      <div class="funnel">
        <div class="funnel-title title">招聘流程分布</div>
        <div class="echarts-funnel">111</div>
      </div>
      <div class="interview">
        <div class="title flex space-between align-center">
          <div>今日面试安排</div>
          <div class="detail">查看详细安排 > </div>
        </div>
      </div>
      <div class="signing">
        <div class="title">职位签约动态</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      keyWord: '', // 搜索关键词
      statusList: [
        { name: '全部', isActive: false, value: '', type: 1 },
        { name: '上架中', isActive: false, value: 2, type: 2 },
        { name: '未上架', isActive: false, value: 3, type: 3 }
      ],
      checked: 1
    }
  },
  mounted() {
    this.statusList[this.checked - 1].isActive = true
  },
  methods: {
    handleSearch() {
      console.log('xxx')
    },
    // 状态tabs
    changeTab(item) {
      console.log(item)
      this.statusList[this.checked - 1].isActive = false
      item.isActive = true
      this.checked = item.type
    }
  }
}
</script>

<style scoped lang="scss">
.workbenche {
  .wrap-l {
    flex: 1;
    margin-right: 16px;
    .count {
      display: flex;
      background-color: #fff;
      margin-bottom: 16px;
      padding: 18px 0;
      .count-item {
        flex: 1;
        text-align: center;
        .count-title {
          font-size: 14px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: #666666;
          line-height: 20px;
        }
        & div:nth-child(2) {
          font-size: 32px;
          font-family: Gilroy-Bold, Gilroy;
          font-weight: bold;
          color: #333333;
          line-height: 40px;
          margin: 8px 0;
        }
        & div:last-child {
          font-size: 12px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: #999999;
          line-height: 17px;
        }
        & div .warning {
          font-size: 32px;
          font-family: Gilroy-Bold, Gilroy;
          font-weight: bold;
          color: #FF8080 !important;
          line-height: 40px;
        }
      }
    }
    .service-list {
      background-color: #fff;
      margin-bottom: 16px;
      padding: 24px 0;
      .service-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        img {
          width: 40px;
          height: 40px;
          background: linear-gradient(140deg, #FFAD96 0%, #FF8383 100%);
          border-radius: 8px;
        }
        div {
          font-size: 14px;
          margin-top: 14px;
          font-family: PingFangSC-Medium, PingFang SC;
          font-weight: 500;
          color: #333333;
          line-height: 20px;
        }
      }
    }
    .position {
      background-color: #fff;
      .position-filter {
        border-bottom: 1px solid #F4F4F4;
        padding: 16px 24px;
        .filter-l {
          max-width: 310px;
        }
        .filter-r {
          .itemActive {
            border: 1px solid #30b0bf !important;
            background: #eaf7f9 !important;
            color: #30b0bf !important;
          }
          .mainItem {
            padding: 5px 22px;
            border-radius: 0px 2px 2px 0px;
            border: 1px solid #cccccc;
            cursor: pointer;
            font-size: 14px;
            font-family: PingFangSC-Regular, PingFang SC;
            font-weight: 400;
            color: #999999;
          }
        }
      }
      .position-list {
        // padding: 16px 16px;
      }
    }
  }
  .wrap-r {
    width: 392px;
    .title {
      padding: 14px 24px;
      font-size: 14px;
      font-family: PingFangSC-Medium, PingFang SC;
      font-weight: 500;
      color: #333333;
      line-height: 20px;
      border: 1px solid #F4F4F4;
    }
    .funnel {
      background-color: #fff;
      height: 223px;
      margin-bottom: 18px;
      .funnel-title {
        // border: 1px solid #F4F4F4;
      }
    }
    .interview {
      background-color: #fff;
      height: 367px;
      margin-bottom: 16px;
      .detail {
        font-size: 14px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: #999999;
        line-height: 20px;
      }
    }
    .signing {
      background-color: #fff;
      height: 185px;
    }
  }
}

.positionInfo {
  // margin: 8px 16px;
  flex: 3.5;
  .infoTop {
    .it_name {
      font-size: 16px;
      font-family: PingFangSC-Medium, PingFang SC;
      font-weight: 600;
      color: #333333;
    }
    .it_tag {
      border-radius: 2px;
      font-size: 12px;
      font-family: PingFangSC-Medium, PingFang SC;
      padding: 2px 8px;
    }
    .tag1 {
      background: #fff0e9;
      color: #ff915f;
      border: 1px solid #ff9d70;
      margin: 0 15px;
    }
    .tag2 {
      background: #eaf7f9;
      border: 1px solid #30B0BF;
      color: #30B0BF;
    }
  }
  .infoCenter {
    font-size: 14px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #999999;
    line-height: 20px;
    margin: 6px 0;
  }
  .infoBottom {
    span {
      font-size: 14px;
      font-family: PingFangSC-Regular, PingFang SC;
      font-weight: 400;
      color: #999999;
    }
  }
}
.positionNum {
  flex: 4;
  .desc {
    margin-bottom: 8px;
    font-size: 14px;
    font-family: PingFangSC-Regular, PingFang SC;
    font-weight: 400;
    color: #999999;
  }
  .num {
    font-size: 16px;
    font-family: PingFangSC-Regular, PingFang SC;
    color: rgba($color: #333, $alpha: 0.9);
  }
  .position-status {
    .onShell {
      font-size: 14px;
      font-family: PingFangSC-Medium, PingFang SC;
      font-weight: 500;
      color: #30B0BF;
      line-height: 20px;
    }
    .offShell {
      font-size: 14px;
      font-family: PingFangSC-Medium, PingFang SC;
      font-weight: 500;
      color: #CCCCCC;
      line-height: 20px;
    }
  }
  .more {
    font-weight: bold;
    color: #30B0BF;
    .el-icon-more {
      color: #30B0BF;
    }
  }
}
.positionContainer {
  margin: 16px;
  border-bottom: 1px solid #f4f4f4;
  &:hover {
    background-color: #eaf7f9;
    transition: all 0.6s;
  }
  padding: 15px 10px;
}
</style>
