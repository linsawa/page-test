<template>
  <div class="studentInfo">
    <div class="info-top">
      <div class="top-l">
        <div class="avater">
          <!-- <img src="https://fuss10.elemecdn.com/8/27/f01c15bb73e1ef3793e64e6b7bbccjpeg.jpeg" alt=""> -->
          <el-carousel v-if="resumeInfo.videoList.length !== 0" trigger="click" height="300px" :autoplay="false" indicator-position="none">
            <el-carousel-item v-for="(item,index) in resumeInfo.videoList" :key="index">
              <video
                class="video-container"
                :src="item.resourcePath"
                controls="controls"
              />
            </el-carousel-item>
          </el-carousel>
          <el-image
            v-else-if="resumeInfo.basicInfo.headImg"
            :src="resumeInfo.basicInfo.headImg"
            fit="fit"
          />
          <el-empty
            v-else
            description="暂无数据"
          />
        </div>
      </div>
      <div class="top-r">
        <div class="basic-info">
          <div>
            <div class="name">
              <span>{{ resumeInfo.basicInfo.name }}</span>
            </div>
            <div class="basic-info-attr">{{ resumeInfo.basicInfo.sex }} | 21岁 | {{ resumeInfo.basicInfo.graduateDate }} | {{ resumeInfo.basicInfo.degree }}</div>
            <div class="basic-info-detailr">
              <div>院校：<span>{{ resumeInfo.basicInfo.instName || '暂未填写' }}</span></div>
              <div>专业：<span>{{ resumeInfo.basicInfo.majorName }}</span></div>
              <div>邮箱：<span>{{ resumeInfo.basicInfo.email }}</span></div>
              <div>电话：<span>{{ resumeInfo.basicInfo.mobile }}</span></div>
            </div>
          </div>
          <div class="rate">
            <div class="rate-title">老师推荐等级</div>
            <el-rate
              v-model="resumeInfo.basicInfo.score"
              disabled
              :max="3"
              :colors="['#30B0BF', '#30B0BF', '#30B0BF']"
              score-template="{value}"
            />
          </div>

        </div>
        <div class="target">
          <div class="item">
            <div>求职意向</div>
            <div>{{ resumeInfo.basicInfo.expJobTips || '暂未填写' }}</div>
          </div>
          <div class="item">
            <div>期望城市</div>
            <div>{{ resumeInfo.basicInfo.expCity || '暂未填写' }}</div>
          </div>
          <div class="item">
            <div>期望薪资</div>
            <div v-if="resumeInfo.basicInfo.expMaxSalary"><span>{{ resumeInfo.basicInfo.expMaxSalary }}</span>元/月</div>
            <div v-else>暂未填写</div>
          </div>
        </div>

      </div>
    </div>
    <div class="info-bottom">
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="学生简历" name="first">
          <div class="pane-content" :style="{ height: height + 'px' }">
            <div class="resume-sections">
              <div class="sections-title">自我评价</div>
              <div class="sections-context">{{ resumeInfo.basicInfo.evaluationSelf }}</div>
            </div>
            <div class="resume-sections">
              <div class="sections-title">主修课程</div>
              <div class="sections-context">{{ resumeInfo.basicInfo.course }}</div>
            </div>
            <div class="resume-sections">
              <div class="sections-title">校园经历</div>
              <div class="sections-context school-life">
                <div v-for="item in resumeInfo.campExpeList" :key="item.id" class="expe-item">
                  <div>
                    <div class="place">{{ item.activity }}</div>
                    <div class="duration">{{ formatDate(item.beginTime) }} - {{ formatDate(item.endTime) }}</div>
                  </div>
                  <div class="role">{{ item.role }}</div>
                  <div class="work-content" v-html="formatContent(item.experience)" />
                </div>
              </div>
            </div>
            <div class="resume-sections">
              <div class="sections-title">实习经历</div>
              <div class="sections-context work-expe">
                <div v-for="item in resumeInfo.internExpeList" :key="item.id" class="expe-item">
                  <div>
                    <div class="place">{{ item.activity }}</div>
                    <div class="duration">{{ formatDate(item.beginTime) }} - {{ formatDate(item.endTime) }}</div>
                  </div>
                  <div class="role">{{ item.role }}</div>
                  <div class="work-content" v-html="formatContent(item.experience)" />
                </div>
              </div>
            </div>
            <div class="resume-sections">
              <div class="sections-title">荣誉证书</div>
              <div class="sections-context">{{ resumeInfo.basicInfo.certificates }}</div>
            </div>
            <div class="resume-sections">
              <div class="sections-title">兴趣爱好</div>
              <div class="sections-context hobbies">
                <div v-if="resumeInfo.basicInfo.hobby" class="tags-list">
                  <!-- <div>{{ resumeInfo.basicInfo.hobby }}</div> -->
                  <el-tag v-for="(item,index) in splitString(resumeInfo.basicInfo.hobby)" :key="index" type="info" size="medium">{{ item }}</el-tag>
                </div>
              </div>
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="学生视频" name="second">
          <div class="pane-content" :style="{ height: height + 'px' }">
            <div class="students-video">
              <div v-if="resumeInfo.videoList.length !== 0" class="video-list">
                <!-- <el-image
                  style="width: 100px; height: 100px"
                  src="https://fuss10.elemecdn.com/8/27/f01c15bb73e1ef3793e64e6b7bbccjpeg.jpeg"
                  :preview-src-list="srcList"
                /> -->
                <div v-for="(item,index) in resumeInfo.videoList" :key="index" class="video-item">
                  <video
                    class="video-container"
                    :src="item.resourcePath"
                    controls="controls"
                  />
                </div>
              </div>
              <el-empty
                v-else
                description="暂无数据"
              />
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="附件简历" name="third">
          <div class="pane-content" :style="{ height: height + 'px' }">
            <el-empty
              description="暂无数据"
            />
            <!-- <div class="annex" /> -->
          </div>
        </el-tab-pane>
        <el-tab-pane label="求职附件" name="fourth">
          <div class="pane-content" :style="{ height: height + 'px' }">
            <el-empty
              description="暂无数据"
            />
            <!-- <div class="file-list">
              <div v-for="i in 30" :key="i" class="file-item">
                <div class="file-img">
                  <img src="@/assets/schoolRecruit/pdf.png" alt="">
                </div>
                <div class="file-info">
                  <div class="file-name">附件名附件名附件名附件名</div>
                  <div class="file-detail">
                    <div class="size">2.1M</div>
                    <div class="download">下载</div>
                  </div>
                </div>
              </div>
            </div> -->
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    height: {
      type: Number,
      default: 0
    },
    resumeInfo: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
      // value: 2.1,
      activeName: 'first',
      swiperList: [
        'https://images.ihccs.com/VCeUAPWNKnZRq1VhGVRVLn.mp4',
        'https://images.ihccs.com/7CUPjCpxJe6S68Q44p679H.mp4',
        'https://images.ihccs.com/XAcBWAACLg2iBjGRtDKfZj.mp4',
        'https://images.ihccs.com/LJ2qefCZKr9vgyBV6JkPea.mp4',
        'https://images.ihccs.com/SrFq6K66UK7MPptTpxKWjz.mp4',
        'https://images.ihccs.com/7CUPjCpxJe6S68Q44p679H.mp4',
        'https://images.ihccs.com/2x5irK8pbbk6ZwHQPw4pA4.mp4'
      ]
    }
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event)
    },

    formatDate(timestamp) {
      const date = new Date(timestamp)
      const year = date.getFullYear()
      const month = date.getMonth() + 1
      return `${year}/${month.toString().padStart(2, '0')}`
    },

    formatContent(text) {
      return text.replace(/\n/g, '<br>')
    },

    splitString(str, separator = /\。|\，|\；|\、/) {
      return str.split(separator)
    }
  }
}
</script>

<style scoped lang="scss">
.studentInfo {
  .info-top {
    display: flex;
    .top-l {
      .avater {
        width: 200px;
        height: 300px;
        // background-color: #999;
        background-color: #f7f8fc;
        position: relative;
        overflow: hidden;
        // img {
        //   width: 100%;
        //   height: 100%;
        // }
        .video-container {
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          min-width: 100%;
          min-height: 100%;
          width: auto;
          height: auto;
          max-width: 100%;
          max-height: 100%;
          object-fit: contain;
        }
      }
    }
    .top-r {
      // display: flex;
      width: 100%;
      margin-left: 24px;
      .basic-info {
        display: flex;
        justify-content: space-between;
        .name {
          font-size: 18px;
          color: #333;
          font-weight: 500;
        }
        .basic-info-attr {
          font-size: 14px;
          font-weight: 400;
          color: #999999;
          margin: 10px 0 24px;
        }
        .basic-info-detailr {
          font-size: 14px;
          font-weight: 400;
          color: #999;
          & div {
            margin-bottom: 10px;
          }
          & span {
            font-weight: 400;
            color: #333;
          }
        }
        .rate {
          display: flex;
          flex-direction: column;
          align-items: end;
          .rate-title {
            font-size: 14px;
            color: #ccc;
            margin-bottom: 8px;
          }
        }
      }
      .target {
        border-radius: 4px;
        background-color: #F8FAFA;
        display: flex;
        padding: 20px;
        margin-top: 42px;
        font-size: 14px;
        color: #999;
        .item {
          margin-right: 57px;
          & div:nth-child(2) {
            color: #333;
            margin-top: 6px;
          }
          & span {
            color: #30B0BF;
            font-weight: bold;
          }
        }
      }
    }
  }
  .info-bottom {
    margin-top: 16px;
    .pane-content {
      overflow-y: auto;
      height: 230px;
    }
    // 学生简历
    .resume-sections {
      margin-bottom: 8px;
      .sections-title {
        padding-left: 10px;
        height: 40px;
        line-height: 40px;
        background-color: #ccc;
        background: #EAF7F9;
        border-radius: 4px;
        font-size: 14px;
        font-weight: 600;
        color: #333333;
      }
      .sections-context {
        // margin-top: 16px;
        padding: 16px;
        font-size: 14px;
        font-weight: 400;
        color: #666666;
        line-height: 22px;
      }
      .school-life {
        background: #F8FAFA;
        border-radius: 4px;
        border: 1px solid #F4F4F4;
        margin: 16px;
      }
      .work-expe {
        background: #F8FAFA;
        border-radius: 4px;
        border: 1px solid #F4F4F4;
        margin: 16px;
      }
      .hobbies {
        .el-tag {
          margin-right: 8px;
        }
      }
      .expe-item {
        margin-bottom: 24px;
        &:last-child {
          margin-bottom: 0px;
        }
        & div:first-child {
          display: flex;
          justify-content: space-between;
          align-items: center;
        }
        .place {
          font-weight: 600;
          color: #333333;
          font-size: 16px;
        }
        .role {
          color: #333333;
          margin: 4px 0 8px;
        }
      }
    }
    // 学生视频
    .students-video {
      .video-list {
        display: flex;
        flex-wrap: wrap;
        .video-item {
          position: relative;
          width: 138px;
          height: 206px;
          border-radius: 5px;
          border: 1px solid #E4E4E4;
          margin-right: 16px;
        }
        .video-container {
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          min-width: 100%;
          min-height: 100%;
          width: auto;
          height: auto;
          max-width: 100%;
          max-height: 100%;
          object-fit: contain;
        }
      }
    }
    // 附件简历
    .annex {
      width: 100%;
      height: 400px;
      background: #F8FAFA;
      border-radius: 4px;
    }

    // 求职附件
    .file-list {
      background-color: #fff;
      display: flex;
      flex-wrap: wrap;
      .file-item {
        // flex-shrink: 0;
        width: 186px;
        height: 80px;
        background: #F8FAFA;
        border-radius: 4px;
        padding: 16px;
        display: flex;
        align-items: center;
        margin-right: 8px;
        margin-top: 8px;
        .file-img {
          width: 48px;
          height: 48px;
          margin-right: 8px;
        }
        .file-info {
          .file-name {
            font-size: 16px;
            display: -webkit-box;
            -webkit-box-orient: vertical;
            -webkit-line-clamp: 1;
            overflow: hidden;
          }
          .file-detail {
            font-size: 14px;
            display: flex;
            justify-content: space-between;
            margin-top: 8px;
            .size {
              color: #999;
            }
            .download {
              font-weight: 400;
              color: #30B0BF;
            }
          }
        }
      }
    }
  }
}
</style>
