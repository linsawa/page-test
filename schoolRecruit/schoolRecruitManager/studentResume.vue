<!-- 学生简历查询 -->
<template>
  <div class="resumeContainer">
    <!-- 搜索 -->
    <div class="search flex space-between">
      <div class="search-form">
        <el-form ref="resumeFrom" :model="resumeFrom" :inline="true">
          <el-form-item label="申请职位" prop="position">
            <el-select v-model="resumeFrom.position" placeholder="按申请职位">
              <el-option
                v-for="item in positionDropdownList"
                :key="item.pid"
                :label="item.name"
                :value="item.name"
              />
            </el-select>
          </el-form-item>
          <el-form-item label="毕业年份" prop="graduationYear">
            <el-date-picker
              v-model="resumeFrom.graduationYear"
              type="year"
              placeholder="选择毕业年份"
            />
          </el-form-item>
          <el-form-item label="学历" prop="degree">
            <el-select v-model="resumeFrom.degree" placeholder="按学历">
              <el-option
                v-for="item in degreeList"
                :key="item.name"
                :label="item.name"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item label="院校专业" prop="major">
            <el-cascader v-model="resumeFrom.major" :options="instOptions" :show-all-levels="false" placeholder="按院校专业" />
          </el-form-item>
          <el-form-item label="老师评级" prop="score">
            <el-select v-model="resumeFrom.score" placeholder="老师推荐等级">
              <el-option
                v-for="item in studentScore"
                :key="item.name"
                :label="item.name"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
        </el-form>
      </div>
      <div class="btns">
        <el-button type="primary" @click="resumeFilter">筛选</el-button>
        <el-button type="primary" plain @click="resetForm('resumeFrom')">重置</el-button>
      </div>

    </div>

    <div class="main">
      <div class="main-left">
        <div class="resume-search">
          <el-input
            v-model="input3"
            placeholder="请输入关键词"
            class="input-with-select"
          >
            <el-button slot="append" icon="el-icon-search" />
          </el-input>
        </div>
        <div class="checkAll" style="display: none">
          <el-checkbox
            v-model="checkedAll"
            style="margin-right: 20px"
            :indeterminate="isIndeterminate"
          >全选</el-checkbox>
          <el-button type="primary" size="mini" plain>批量匹配</el-button>
        </div>
        <div
          ref="listContainer"
          style="overflow-y: auto; margin-top: 20px"
          class="resume-list"
          @scroll="handleScroll"
        >
          <studentItem
            v-for="item in allResumeList"
            :key="item.uid"
            :resume-item="item"
            :selected-uid="selectedResumeItemUid"
            @itemClick="handleResumeClick"
          />
        </div>
      </div>
      <div class="main-right">
        <studentInfo :height="230" :resume-info="studentResumeInfo" />
        <div class="select-position" style="display: none">
          <div class="select-position-l">
            <div v-for="item in 10" :key="item" class="position-item">
              <div class="item-l">
                <div>PHP开发工程师</div>
                <div>上海 | 本科</div>
              </div>
              <div class="item-r">
                <el-tag type="primary" plain size="mini">已申请</el-tag>
              </div>
            </div>
          </div>
          <div class="select-position-r">
            <el-button type="primary" size="medium" @click="drawer = true">匹配其他岗位</el-button>
          </div>
        </div>
      </div>
    </div>

    <!-- 抽屉 -->
    <el-drawer
      ref="drawerRef"
      title="我是标题"
      :visible.sync="drawer"
      direction="rtl"
      :with-header="false"
      :modal="false"
      :size="358"
    >
      <div class="matchPosition">
        <!-- 关闭按钮 -->
        <div class="pd-Top flex align-center" @click="closeDrawer">
          <hamburger :is-active="ishamburger" class="cursor-poi" :isp="true" />
        </div>
        <div class="tips">
          为
          <img src="https://cube.elemecdn.com/3/7c/3ea6beec64369c2642b92c6726f1epng.png" alt="">
          <span>栗云</span>
          匹配岗位
        </div>
        <div class="drawer-search">
          <el-input
            v-model="input3"
            placeholder="请输入关键词"
            class="input-with-select"
          >
            <el-button slot="append" icon="el-icon-search" />
          </el-input>
        </div>
        <div class="drawer-position">
          <div v-for="i in 10" :key="i" class="drawer-position-item">
            <div class="position-item-l">
              <div>PHP开发工程师</div>
              <div>
                <span>上海</span> |
                <span>本科</span> |
                <span>6000</span>元/月
              </div>
              <div>U职上海分公司</div>
            </div>
            <div class="position-item-r">
              <el-button type="primary" size="medium">匹配</el-button>
            </div>
          </div>
        </div>
      </div>
    </el-drawer>

  </div>
</template>

<script>
import studentItem from '@/views/schoolRecruit/components/studentItem.vue'
import studentInfo from '@/views/schoolRecruit/components/studentInfo.vue'
import Hamburger from '@/components/Hamburger'
import {
  resumeList,
  positionDropdownList,
  instituteCollegeList,
  schooList,
  stuBasicInfo,
  stuCampExpeList,
  stuInternExpeList,
  relationMedia
} from '@/api/schoolRecruit'
import { enumSearch } from '@/api/common'
import { mapGetters } from 'vuex'

export default {
  components: {
    studentItem,
    studentInfo,
    Hamburger
  },
  data() {
    return {
      resumeFrom: {
        position: '',
        score: '',
        graduationYear: '',
        degree: '',
        major: ''
      },
      input3: '',
      ishamburger: false,
      positionOptions: [
        {
          name: '111',
          value: 1111
        }
      ],
      value: 2.1,
      drawer: false, // 抽屉开启的状态
      positionDropdownList: [], // 申请职位的下拉列表
      studentScore: [
        { name: '未评级', value: 0 },
        { name: '一星', value: 1 },
        { name: '二星', value: 2 },
        { name: '三星', value: 3 }
      ],
      degreeList: [], // 学历列表
      resumeList: [], // 简历列表
      allResumeList: [],
      pageSize: 5, // 每页数量
      pageIndex: 1, // 当前页码
      currentResume: '', // 当前显示的简历内容
      // isIndeterminate: false // 是否半选
      instOptions: [], // // 院校专业列表
      studentResumeInfo: {
        basicInfo: {},
        campExpeList: [],
        internExpeList: [],
        videoList: []
      },
      resumeTotal: 0,
      selectedResumeItemUid: null,
      selectedResumeItemInstid: ''
    }
  },
  computed: {
    ...mapGetters(['userInfo', 'compInfo']),

    // 全选状态
    checkedAll: {
      set(val) {
        this.resumeList.forEach(item => (item.ischecked = val))
      },
      get() {
        // return (
        //   this.resumeList.filter(item => item.ischecked).length === this.resumeList.length
        // )
        const checkedCount = this.resumeList.filter(item => item.ischecked).length
        if (checkedCount === 0) {
          // 没有选中任何项
          return false
        } else if (checkedCount === this.resumeList.length) {
          // 全部选中
          return true
        } else {
          // 部分选中
          return null
        }
      }
    },
    // 半选状态
    isIndeterminate() {
      return !this.checkedAll && this.resumeList.some(item => item.ischecked)
    }
  },
  mounted() {
    this.getPositionDropdownList()
    this.getResumeList()
    this.getSalaryLists('学历')
    this.currentResume = this.resumeList[0]
    this.getSchoolList()
    this.getStuBasicInfo()
  },
  methods: {
    // 点击图标关闭抽屉
    closeDrawer() {
      this.$refs.drawerRef.closeDrawer()
    },

    // 获取当前企业发布的职位列表
    getPositionDropdownList() {
      positionDropdownList({
        // compId: '',
        compId: this.compInfo.company.compId,
        orgId: '',
        status: ''
      }).then(res => {
        console.log('职位下拉列表', res)
        this.positionDropdownList = res.data
      })
    },

    // 获取学生简历列表
    getResumeList() {
      resumeList({
        degree: this.resumeFrom.degree, // 学历
        getResumeDeliveryList: true,
        graduateDate: this.resumeFrom.graduationYear, // 毕业年份
        majorName: this.resumeFrom.major[2], // 专业名称
        // name: '',
        pageIndex: this.pageIndex,
        pageSize: this.pageSize,
        score: this.resumeFrom.score, // 老师评级
        status: 1
      }).then(res => {
        console.log('第' + this.pageIndex + '页学生简历列表', res)
        if (res.code === 200) {
          this.resumeList = res.data.results
          this.resumeTotal = res.data.total
          this.allResumeList = [...this.allResumeList, ...this.resumeList]
          // this.resumeList.map(item => {
          //   this.$set(item, 'ischecked', false)
          // })
          this.allResumeList.map(item => {
            this.$set(item, 'ischecked', false)
          })
          this.selectedResumeItemUid = this.allResumeList[0].uid
          this.selectedResumeItemInstid = this.allResumeList[0].instId
          this.getStuBasicInfo()
        }
      })
    },

    // 获取学历列表
    async getSalaryLists(name) {
      const data = { code: '学历' }
      const res = await enumSearch(data)
      if (res.code === 200) {
        this.degreeList = res.data
        console.log('学历列表', res)
      } else {
        this.$message.error(`获取${name}数据失败`)
      }
    },

    resumeFilter() {
      this.getResumeList()
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    },

    // 获取学校院系专业的树形结构
    async getSchoolList() {
      const arr = []
      const instRes = await schooList({})
      instRes.data.results.forEach(async item => {
        console.log('instRes', instRes)
        const instItem = {}
        instItem.label = item.instName
        instItem.value = item.instName
        instItem.children = []
        const colRes = await instituteCollegeList({ instId: item.instId })
        console.log('colRes', colRes)

        const collegeList = colRes.data.map(i => ({
          label: i.collegeName,
          value: i.collegeName,
          children: i.majorList.map(el => ({
            label: el.majorName,
            value: el.majorName
          }))
        }))
        instItem.children = collegeList
        arr.push(instItem)
      })
      this.instOptions = arr
    },

    // 获取学生基本信息
    async getStuBasicInfo() {
      const res = await stuBasicInfo({
        instId: this.selectedResumeItemInstid,
        uid: this.selectedResumeItemUid
      })
      console.log('学生详细信息', res)
      this.studentResumeInfo.basicInfo = res.data
      this.getCampStuExpeList()
      this.getStuInternExpeList()
      this.getRelationMedia()
    },

    // 获取学生校内经历
    async getCampStuExpeList() {
      const res = await stuCampExpeList({
        instId: this.selectedResumeItemInstid,
        uid: this.selectedResumeItemUid
      })
      console.log('学生校内经历', res)
      this.studentResumeInfo.campExpeList = res.data
    },

    // 获取学生实习经历
    async getStuInternExpeList() {
      const res = await stuInternExpeList({
        instId: this.selectedResumeItemInstid,
        uid: this.selectedResumeItemUid
      })
      console.log('学生实习经历', res)
      this.studentResumeInfo.internExpeList = res.data
    },

    // 获取学生上传的视频简历
    async getRelationMedia() {
      const res = await relationMedia(this.selectedResumeItemUid)
      console.log('简历对应的视频图片', res)
      this.studentResumeInfo.videoList = res.data
    },

    // 触底加载
    handleScroll() {
      const container = this.$refs.listContainer
      // const container = this.$refs[ref]
      // 如果容器的滚动位置到达了底部，则加载新的内容
      // console.log('container', container.scrollTop)
      // if (container && (container.scrollTop + container.clientHeight >= container.scrollHeight)) {
      //   // this.loadMoreContent()
      //   console.log('触底了')
      //   if (this.allResumeList.length < this.resumeTotal) {
      //     console.log('总数', this.resumeTotal, this.allResumeList.length)
      //     this.pageIndex += 1
      //     this.getResumeList()
      //     this.allResumeList = [...this.allResumeList, ...this.resumeList]
      //   }
      // }
      // 如果到了底部，且还有数据未加载，则获取下一页的数据
      if (container.scrollTop + container.clientHeight >= container.scrollHeight && this.allResumeList.length < this.resumeTotal) {
        this.pageIndex += 1
        this.getResumeList()
      }
    },

    handleResumeClick(data) {
      console.log('uid', data.uid)
      this.allResumeList.forEach(item => {
        item.ischecked = false
        if (item.uid === data.uid) {
          item.ischecked = true
        }
      })
      this.selectedResumeItemUid = data.uid
      this.selectedResumeItemInstid = data.instId
      this.getStuBasicInfo()
    }

  }
}
</script>

<style scoped lang="scss">
.resumeContainer {
  display: flex;
  height: calc(100vh - 116px); // naverbar 60px + 上线padding 12px + 面包屑导航32px
  flex-direction: column;
  .search {
    background: #ffffff;
    border-radius: 4px;
    padding: 20px 16px 0;
    .search-form {
      width: 1100px;
    }
    .btns {
      width: 200px;
      flex-wrap: nowrap;
    }
  }
  .main {
    margin-top: 8px;
    overflow: hidden;
    // flex: 1;
    display: flex;
    // background-color: red;

    .main-left {
      // flex: 1;
      width: 380px;
      height: 100%;
      position: relative;
      // width: 390px;
      // background-color: #ccc;
      background: #ffffff;
      border-radius: 4px;
      // padding: 20px 16px;
      // padding: 20px 0;
      margin-right: 2px;
      padding-bottom: 64px;
      display: flex;
      flex-direction: column;
      .resume-search {
        padding: 20px 16px;
        border-bottom: 1px solid #F4F4F4;
      }
      .checkAll {
        display: flex;
        padding: 0 16px;
        align-items: center;
        justify-content: space-between;
        // width: 100%;
        height: 64px;
        position: absolute;
        z-index: 9;
        bottom: 0;
        left: 0;
        right: 0;
        background-color: #fff;
        // box-shadow:0px 0px 0px gray
        border-top: 1px solid #F4F4F4;
      }
      .resume-list {
        flex: 1
      }
    }
    .main-right {
      position: relative;
      flex: 1;
      padding: 24px;
      background: #ffffff;
      border-radius: 4px;
      padding-bottom: 68px;
      .select-position {
        position: absolute;
        bottom: 0;
        left: 0;
        right: 0;
        padding: 0 24px;
        height: 64px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        background-color: #fff;
        .select-position-l {
          display: flex;
          width: 100%;
          overflow-x: scroll;
          overflow: hidden;
          margin-right: 12px;
          &:hover {
            overflow: auto;
          }
          &::-webkit-scrollbar {
            width: 7px;
            height: 7px;
            background-color: #f5f5f5;
          }
          &::-webkit-scrollbar-track {
            box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
            -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
            border-radius: 10px;
            background-color: #f5f5f5;
          }
          &::-webkit-scrollbar-thumb {
            border-radius     : 10px;
            box-shadow        : inset 0 0 6px rgba(0, 0, 0, 0.1);
            -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
            background-color  : #c8c8c8;
          }

          .position-item {
            display: flex;
            flex-shrink: 0;
            align-items: center;
            justify-content: space-between;
            width: 223px;
            height: 48px;
            padding: 0 16px;
            background: #F8FAFA;
            border-radius: 4px;
            // border: 1px solid #F4F4F4;
            margin-right: 8px;
            .item-l {
              flex-shrink: 0;
              & div:nth-child(1) {
                font-size: 14px;
                font-weight: 500;
                color: #333333;
              }
              & div:nth-child(2) {
                font-size: 12px;
                font-weight: 400;
                color: #999;
                margin-top: 2px;
              }
            }
          }
        }
      }
    }
  }
  .el-drawer__wrapper {
    position: fixed;
    top: 60px;
    right: 0;
    bottom: 0;
    left: 0;
    overflow: hidden;
    margin: 0;
    height: calc(100vh - 60px);
  }
  .matchPosition {
    padding: 20px;
    .tips {
      font-size: 14px;
      color: #333333;
      display: flex;
      align-items: center;
      margin: 20px 0 24px;
      img {
        width: 20px;
        height: 20px;
        margin: 0 4px;
      }
      span {
        color: #000;
        margin-right: 4px;
      }
    }
    .drawer-search {
      margin-bottom: 16px;
    }
    .drawer-position {
      .drawer-position-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 16px;
        height: 101px;
        background: #F8FAFA;
        border-radius: 4px;
        border: 1px solid #F4F4F4;
        margin-bottom: 8px;
        .position-item-l {
          font-size: 14px;
          color: #999;
          & div:first-child {
            font-size: 16px;
            color: #333333;
          }
          & div:nth-child(2) {
            margin: 4px 0 6px;
            span:last-child {
              color: #30B0BF;
              font-weight: Bold;
            }
          }
        }
      }
    }

  }
}
</style>
