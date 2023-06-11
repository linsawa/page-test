<!-- 学生招聘管理 -->
<template>
  <div class="recruitContainer">
    <div class="recruit-l" :class="{ 'position-collapse': isCollapse }">
      <div class="position-section">
        <div v-show="isCollapse" class="collapse-title">
          <div><i class="el-icon-s-unfold" @click="toggle" /></div>
          <div>展开岗位</div>
        </div>
        <div v-show="!isCollapse" class="positions-top">
          <div class="title">
            <div class="title-l">
              <div>招聘岗位</div>
              <div>共<span>{{ positionTotal }}</span>个岗位</div>
            </div>
            <div class="title-r">
              <span>收起</span>
              <i class="el-icon-s-fold" @click="toggle" />
            </div>
          </div>
          <div class="position-search">
            <el-input
              v-model="positionSearch"
              placeholder="请输入关键词"
              class="input-with-select"
            >
              <el-button slot="append" icon="el-icon-search" @click="positionKeywordSearch" />
            </el-input>
          </div>
        </div>
        <div
          v-show="!isCollapse"
          ref="positionListContainer"
          class="position-list"
          @scroll="handlePositionListScroll"
        >
          <positionItem
            v-for="(item,index) in positionList"
            :key="index"
            :info="item"
            :selected-pid="selectedPositionItemPid"
            @itemClick="positionItemClick"
          />
        </div>
      </div>
    </div>
    <div class="recruit-r">
      <!-- 面包屑导航 -->
      <Breadcrumb />
      <!-- 筛选区域 -->
      <div class="filter-section">
        <div class="filter-section-top">
          <!-- tabs -->
          <div class="tabs">
            <div
              v-for="item in tabsList"
              :key="item.name"
              :class="item.active ? 'active' : ''"
              class="isSelect"
              @click="changeTab(item)"
            >
              {{ item.name }}({{ item.value }})
            </div>
          </div>
          <!-- 搜索 -->
          <div class="search">
            <el-row>
              <el-col :span="20">
                <el-form ref="resumeFrom" :model="resumeFrom" :inline="true" class="resumeFilterFrom">
                  <el-form-item label="申请职位" prop="position" style="display: none">
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
                      :clearable="false"
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
              </el-col>
              <el-col :span="4">
                <div class="btns">
                  <el-button type="primary" @click="resumeFilter">筛选</el-button>
                  <el-button type="primary" plain @click="resetForm('resumeFrom')">重置</el-button>
                </div>
              </el-col>
            </el-row>
          </div>
        </div>
        <!-- 筛选主体区域 -->
        <div class="filter-main">
          <div v-if="studentResumeList.length !== 0" class="main-left">
            <div class="resume-search">
              <el-input
                v-if="checked !== 3"
                v-model="resumeSearch"
                size="medium"
                placeholder="请输入关键词"
                class="input-with-select"
              >
                <el-button slot="append" size="mini" icon="el-icon-search" @click="searchByName" />
              </el-input>
              <div v-if="checked === 3" class="flex space-between align-center">
                <el-input
                  v-model="resumeSearch"
                  style="width: 180px"
                  size="medium"
                  placeholder="请输入关键词"
                  class="input-with-select"
                >
                  <el-button slot="append" icon="el-icon-search" @click="searchByName" />
                </el-input>
                <div class="filter-status flex align-center">
                  <span
                    :class="{ filterStatusSelected: filterStatus === 1 }"
                    @click="switchClick(1,9)"
                  >初筛通过</span>
                  <span
                    :class="{ filterStatusSelected: filterStatus === 2 }"
                    @click="switchClick(2,-1)"
                  >未通过</span>
                </div>
              </div>
            </div>
            <div v-if="checked !== 1 && checked !== 4" class="checkAll">
              <el-checkbox
                v-model="checkedAll"
                style="margin-right: 20px"
                :indeterminate="isIndeterminate"
              >全选</el-checkbox>
              <div v-if="checked === 2" class="check-btns">
                <el-button
                  v-if="checkedAll || isIndeterminate"
                  type="primary"
                  size="mini"
                  plain
                  @click="handleApproveResumeBatch(1)"
                >
                  批量通过
                </el-button>
                <el-button
                  v-if="checkedAll || isIndeterminate"
                  type="primary"
                  size="mini"
                  plain
                  @click="handleApproveResumeBatch(-1)"
                >
                  批量拒绝
                </el-button>
              </div>
              <div v-if="checked === 3" class="check-btns">
                <el-button
                  type="primary"
                  size="mini"
                  plain
                >批量面试邀约</el-button>
              </div>
            </div>
            <div
              ref="resumeListContainer"
              class="resume-list"
              @scroll="handleResumeListScroll"
            >
              <studentItemV2
                v-for="(item, i) in studentResumeList"
                :key="i"
                :resume-item="item"
                :selected-uid="selectedResumeItemUid"
                @itemClick="handleResumeClick"
              />
            </div>
          </div>
          <div v-else class="main-left">
            <el-empty
              description="暂无数据"
            />
          </div>
          <div v-if="studentResumeList.length !== 0" class="main-right">
            <div
              v-if="checked === 4"
              class="interview flex space-between align-center"
            >
              <div>
                <div class="interview-item flex">
                  <img src="@/assets/schoolRecruit/time.png" alt="">
                  <div>面试时间</div>
                </div>
                <div class="mt-1">{{ studentResumeInfo.currentResume.interviewTime }}</div>
              </div>
              <div>
                <div class="interview-item flex">
                  <img src="@/assets/schoolRecruit/position.png" alt="">
                  <div>面试地点</div>
                </div>
                <div class="interview-position mt-1">
                  上海市闵行区七莘路4228号
                </div>
              </div>
              <div>
                <el-button
                  type="primary"
                  plain
                  size="medium"
                >重新安排面试</el-button>
              </div>
            </div>
            <studentInfo
              :height="400"
              :resume-info="studentResumeInfo"
            />
            <div v-if="checked === 2" class="mark-btns">
              <el-button type="primary" @click="handleApproveResume(1)">通过初筛</el-button>
              <el-button type="danger" @click="handleApproveResume(-1)">不合适</el-button>
              <el-button type="primary" plain @click="setRecruitTags">设置标签</el-button>
            </div>
            <div v-if="checked === 3" class="mark-btns">
              <el-button type="primary" @click="openDialogForm('面试邀约', 1)">面试邀约</el-button>
              <el-button type="danger" @click="handleApproveResume(-1)">不合适</el-button>
            </div>
            <div v-if="checked === 4" class="mark-btns">
              <el-button v-if="studentResumeInfo.currentResume.interviewType === 2" type="primary">视频面试</el-button>
              <el-button type="primary" @click="openDialogForm('面试评价',2)">面试评价</el-button>
              <!-- <div v-if="showReviewTags" class="review">
                <i class="el-icon-close" @click="showReviewTags=false" />
                <div>
                  <el-tag
                    v-for="tag in dynamicTags"
                    :key="tag"
                    closable
                    :disable-transitions="false"
                    @close="handleClose(tag)"
                  >
                    {{ tag }}
                  </el-tag>
                  <el-input
                    v-if="inputVisible"
                    ref="saveTagInput"
                    v-model="inputValue"
                    class="input-new-tag"
                    size="small"
                    @keyup.enter.native="handleInputConfirm"
                    @blur="handleInputConfirm"
                  />
                  <el-button v-else class="button-new-tag" size="small" @click="showInput">+ New Tag</el-button>
                </div>
              </div> -->
            </div>
          </div>
          <div v-else class="main-right">
            <el-empty
              description="暂无数据"
            />
          </div>
        </div>
      </div>
    </div>

    <!-- 设置标签对话框 -->
    <el-dialog
      title="招聘标签"
      :visible.sync="showSetRecruitDialog"
      width="40%"
      class="setTagDialog"
      @close="closeSetRecruitDialog"
    >
      <div class="dialog-container">
        <div class="select-tags flex space-between">
          <div class="content-l">
            <div>
              <div class="cate-tags">常用标签</div>
              <!-- <div class="cate-tags">常用标签</div> -->
            </div>
          </div>
          <div class="content-r">
            <div v-for="item in tagsList" :key="item.value" class="tagsListItem">{{ item.name }}</div>
          </div>
        </div>
        <div class="showTags flex align-center">
          <div>已选标签：</div>
          <div class="selectedTags">
            <el-tag
              v-for="tag in dynamicTags"
              :key="tag"
              closable
              :disable-transitions="false"
              @close="handleClose(tag)"
            >
              {{ tag }}
            </el-tag>
            <el-input
              v-if="inputVisible"
              ref="saveTagInput"
              v-model="inputValue"
              class="input-new-tag"
              size="small"
              @keyup.enter.native="handleInputConfirm"
              @blur="handleInputConfirm"
            />
            <el-button v-else class="button-new-tag" size="small" @click="showInput">+ New Tag</el-button>
          </div>
        </div>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="closeSetRecruitDialog">取 消</el-button>
        <el-button type="primary" @click="confirmSetRecruitDialog">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 面试邀约对话框 -->
    <el-dialog
      :title="DialogFormTitle"
      :visible.sync="showInterviewInvitationDialog"
      width="560px"
      class="interviewInvitationDialog"
      @close="cancelIntervieInvitationDialog"
    >
      <div class="container">
        <div class="resume-info flex space-between">
          <div class="resume-l flex align-center">
            <img
              :src="studentResumeInfo.basicInfo.headImg
              "
              alt=""
            >
            <div>
              <div>{{ studentResumeInfo.basicInfo.name }}</div>
              <div>{{ studentResumeInfo.basicInfo.graduateDate
              }} | {{ studentResumeInfo.basicInfo.majorName }}</div>
            </div>
          </div>
          <div class="resume-r">
            <el-rate
              :value="3"
              disabled
            />
          </div>
        </div>
        <div class="position-info flex space-between">
          <div class="position-l">
            <div>{{ studentResumeInfo.currentPosition.name }}</div>
            <div>{{ studentResumeInfo.currentPosition.workingCity }} | {{ studentResumeInfo.currentPosition.degree || "学历不限" }} | {{ studentResumeInfo.currentPosition.compNickName }}</div>
          </div>
          <div class="position-r"><span>6000</span>元/月</div>
        </div>
        <div class="intervie-form">
          <!-- <el-form
            ref="interviewInvitationFormRef"
            :model="interviewInvitationForm"
            label-position="right"
            label-width="80px"
          >
            <el-form-item label="是否参与" prop="interviewType">
              <el-radio-group v-model="interviewInvitationForm.interviewType">
                <el-radio :label="2">参与面试</el-radio>
                <el-radio :label="1">未参与面试</el-radio>
              </el-radio-group>
            </el-form-item>

            <el-form-item class="interviewRate">
              <div class="rate">
                <div class="rete-title flex space-between">
                  <div>面试评分</div>
                  <div>综合得分 <span>3.4</span></div>
                </div>
                <div class="rate-main">
                  <div class="flex space-between">
                    <div class="flex align-center">形象<el-rate v-model="interviewInvitationForm.interviewType" /></div>
                    <div class="flex align-center">谈吐<el-rate v-model="interviewInvitationForm.interviewType" /></div>
                    <div class="flex align-center">态度<el-rate v-model="interviewInvitationForm.interviewType" /></div>
                  </div>
                  <div class="flex space-between">
                    <div class="flex align-center">智力<el-rate v-model="interviewInvitationForm.interviewType" /></div>
                    <div class="flex align-center">性格<el-rate v-model="interviewInvitationForm.interviewType" /></div>
                    <div class="flex align-center">能力<el-rate v-model="interviewInvitationForm.interviewType" /></div>
                  </div>
                </div>
              </div>
            </el-form-item>

            <el-form-item label="面试评价" prop="remark">
              <el-input v-model="interviewInvitationForm.remark" placeholder="例如：请带好简历和证件" />
            </el-form-item>

            <el-form-item label="面试结果" prop="interviewType">
              <el-radio-group v-model="interviewInvitationForm.interviewType">
                <el-radio :label="2">通过面试</el-radio>
                <el-radio :label="1">淘汰</el-radio>
              </el-radio-group>
            </el-form-item>

            <el-form-item label="是否复试" prop="interviewType">
              <el-radio-group v-model="interviewInvitationForm.interviewType">
                <el-radio :label="2">安排复试</el-radio>
                <el-radio :label="1">不再安排</el-radio>
              </el-radio-group>
            </el-form-item>

            <el-form-item label="是否复邀" prop="interviewType">
              <el-radio-group v-model="interviewInvitationForm.interviewType">
                <el-radio :label="2">重新邀约</el-radio>
                <el-radio :label="1">不再邀约</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="面试方式" prop="interviewType">
              <el-radio-group v-model="interviewInvitationForm.interviewType">
                <el-radio :label="2">视频面试</el-radio>
                <el-radio :label="1">线下面试</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="面试时间" prop="dateTime">
              <el-date-picker
                v-model="interviewInvitationForm.dateTime"
                type="datetime"
                placeholder="请选择面试时间"
              />
            </el-form-item>
            <el-form-item v-if="interviewInvitationForm.interviewType !== 2" label="面试地点">
              <el-form-item prop="area">
                <el-cascader
                  ref="addPoint"
                  v-model="interviewInvitationForm.area"
                  size="large"
                  class="w-40"
                  placeholder="请选择省市区"
                  :props="cityProps"
                  :options="cityOptions"
                  clearable
                  @change="handleChange"
                />
              </el-form-item>
              <el-form-item prop="address">
                <el-input
                  v-model="interviewInvitationForm.address"
                  placeholder="请输入面试具体地址"
                />
              </el-form-item>
            </el-form-item>
            <el-form-item label="联系人员" prop="contacter">
              <el-input v-model="interviewInvitationForm.contacter" placeholder="请输入联系人姓名" />
            </el-form-item>
            <el-form-item label="电话" prop="contacterTel">
              <el-input v-model="interviewInvitationForm.contacterTel" placeholder="请输入联系电话" />
            </el-form-item>
            <el-form-item label="其他信息" prop="remark">
              <el-input v-model="interviewInvitationForm.remark" placeholder="例如：请带好简历和证件" />
            </el-form-item>
          </el-form> -->

          <el-form
            ref="interviewInvitationFormRef"
            :model="interviewInvitationForm"
            label-position="right"
            label-width="80px"
          >
            <el-form
              v-if="dialogFormStatus === 2"
              label-position="right"
              label-width="80px"
            >
              <el-form-item label="是否参与" prop="remarkStatus">
                <el-radio-group v-model="interviewInvitationForm.remarkStatus">
                  <el-radio :label="''">参与面试</el-radio>
                  <el-radio :label="-5">未参与面试</el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form-item label="是否复邀" prop="isReInvitation">
                <el-radio-group v-model="interviewInvitationForm.isReInvitation">
                  <el-radio :label="1">重新邀约</el-radio>
                  <el-radio :label="0">不再邀约</el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form
                v-if="dialogFormStatus === 2 && interviewInvitationForm.remarkStatus ===''"
                label-position="right"
                label-width="80px"
              >
                <el-form-item class="interviewRate">
                  <div class="rate">
                    <div class="rete-title flex space-between">
                      <div>面试评分</div>
                      <div>综合得分 <span>{{ calcScore }}</span></div>
                    </div>
                    <div class="rate-main">
                      <div class="flex space-between">
                        <div class="flex align-center">形象<el-rate v-model="interviewInvitationForm.score[1]" /></div>
                        <div class="flex align-center">谈吐<el-rate v-model="interviewInvitationForm.score[2]" /></div>
                        <div class="flex align-center">态度<el-rate v-model="interviewInvitationForm.score[3]" /></div>
                      </div>
                      <div class="flex space-between">
                        <div class="flex align-center">智力<el-rate v-model="interviewInvitationForm.score[4]" /></div>
                        <div class="flex align-center">性格<el-rate v-model="interviewInvitationForm.score[5]" /></div>
                        <div class="flex align-center">能力<el-rate v-model="interviewInvitationForm.score[6]" /></div>
                      </div>
                    </div>
                  </div>
                </el-form-item>

                <el-form-item label="面试评价" prop="interviewRemark">
                  <el-input v-model="interviewInvitationForm.interviewRemark" placeholder="例如：请带好简历和证件" />
                </el-form-item>

                <el-form-item label="面试结果" prop="interviewResult">
                  <el-radio-group v-model="interviewInvitationForm.interviewResult">
                    <el-radio :label="3">通过面试</el-radio>
                    <el-radio :label="-2">淘汰</el-radio>
                  </el-radio-group>
                </el-form-item>

                <el-form-item label="是否复试" prop="isRetest">
                  <el-radio-group v-model="interviewInvitationForm.isRetest">
                    <el-radio :label="1">安排复试</el-radio>
                    <el-radio :label="0">不再安排</el-radio>
                  </el-radio-group>
                </el-form-item>
              </el-form>

            </el-form>

            <el-form
              v-if="dialogFormStatus === 1 || (dialogFormStatus === 2 && interviewInvitationForm.remarkStatus === -5)"
              label-position="right"
              label-width="80px"
            >
              <el-form-item label="面试方式" prop="interviewType">
                <el-radio-group v-model="interviewInvitationForm.interviewType">
                  <el-radio :label="2">视频面试</el-radio>
                  <el-radio :label="1">线下面试</el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form-item label="面试时间" prop="dateTime">
                <el-date-picker
                  v-model="interviewInvitationForm.dateTime"
                  type="datetime"
                  placeholder="请选择面试时间"
                />
              </el-form-item>
              <el-form-item v-if="interviewInvitationForm.interviewType !== 2" label="面试地点">
                <el-form-item prop="area">
                  <el-cascader
                    ref="addPoint"
                    v-model="interviewInvitationForm.area"
                    size="large"
                    class="w-40"
                    placeholder="请选择省市区"
                    :props="cityProps"
                    :options="cityOptions"
                    clearable
                    @change="handleChange"
                  />
                </el-form-item>
                <el-form-item prop="address">
                  <el-input
                    v-model="interviewInvitationForm.address"
                    placeholder="请输入面试具体地址"
                  />
                </el-form-item>
              </el-form-item>
              <el-form-item label="联系人员" prop="contacter">
                <el-input v-model="interviewInvitationForm.contacter" placeholder="请输入联系人姓名" />
              </el-form-item>
              <el-form-item label="电话" prop="contacterTel">
                <el-input v-model="interviewInvitationForm.contacterTel" placeholder="请输入联系电话" />
              </el-form-item>
              <el-form-item label="其他信息" prop="remark">
                <el-input v-model="interviewInvitationForm.remark" placeholder="例如：请带好简历和证件" />
              </el-form-item>
            </el-form>
          </el-form>

        </div>
      </div>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelIntervieInvitationDialog">取 消</el-button>
        <el-button type="primary" @click="confirmIntervieInvitationDialog">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
import positionItem from '../components/positionItem.vue'
import studentItemV2 from '@/views/schoolRecruit/components/studentItemV2.vue'
import studentInfo from '@/views/schoolRecruit/components/studentInfo.vue'
import Breadcrumb from '@/components/Breadcrumb'
import {
  positionList,
  resumeList,
  positionDropdownList,
  instituteCollegeList,
  schooList,
  receivedResumeList,
  stuBasicInfo,
  stuCampExpeList,
  stuInternExpeList,
  relationMedia,
  approveResumeBatch,
  approveResume,
  interview,
  interviewBatch,
  resumeReceivedDetail,
  invitation,
  interviewRemark
} from '@/api/schoolRecruit'
import { getCityOptions } from '@/utils/threeApi'
import { enumSearch } from '@/api/common'
import { mapGetters } from 'vuex'

export default {
  components: {
    positionItem,
    studentItemV2,
    studentInfo,
    Breadcrumb
  },
  data() {
    return {
      isCollapse: false,
      resumeFrom: {
        position: '',
        score: '',
        graduationYear: '',
        degree: '',
        major: ''
      }, // 搜索表单
      cityProps: {
        value: 'name',
        label: 'name',
        children: 'districts'
      },
      tabsList: [
        { name: '全部', value: 1, active: false },
        { name: '待审核', value: 2, active: false },
        { name: '已审核', value: 3, active: false },
        { name: '待面试', value: 4, active: false },
        { name: '已面试', value: 5, active: false },
        { name: '待复试', value: 6, active: false },
        { name: '待签约', value: 7, active: false },
        { name: '已签约', value: 8, active: false },
        { name: '拒绝签约', value: 9, active: false }
      ],
      checked: 1,
      filterStatus: 1, // 简历筛选状态：通过/未通过
      dynamicTags: ['沟通顺畅', '沟通顺畅'],
      inputVisible: false,
      inputValue: '',
      showReviewTags: false,
      positionOptions: [{ name: '', value: '' }],
      positionSearch: '', // 职位搜索关键词
      resumeSearch: '', // 简历搜索关键字
      positionList: [], // 职位列表
      positionTotal: 0, // 岗位总数
      positionPageIndex: 1,
      positionPageSize: 10,
      degreeList: [], // 学历列表
      positionDropdownList: [], // 申请职位的下拉列表
      studentScore: [
        { name: '未评级', value: 0 },
        { name: '一星', value: 1 },
        { name: '二星', value: 2 },
        { name: '三星', value: 3 }
      ],
      resumeStatus: '',
      studentResumeList: [],
      studentResumeListTotal: 0,
      resumeListPageIndex: 1,
      resumeListPageSize: 10,
      instOptions: [], // 院校专业列表
      showSetRecruitDialog: false, // 是否显示设置招聘标签对话框
      studentResumeInfo: {
        basicInfo: {},
        campExpeList: [],
        internExpeList: [],
        videoList: [],
        currentPosition: {},
        currentResume: {}
      },
      selectedPositionItemPid: '',
      selectedResumeItemInstid: '',
      selectedResumeItemUid: null,
      selectedResumeItemReceivedId: null,
      tagsList: [
        { name: '沟通顺畅', value: 1 },
        { name: '沟通顺畅', value: 2 },
        { name: '沟通顺畅', value: 3 },
        { name: '沟通顺畅', value: 4 },
        { name: '沟通顺畅', value: 5 }
      ],
      showInterviewInvitationDialog: false,
      interviewInvitationForm: {
        interviewType: '',
        dateTime: '',
        area: '',
        address: '',
        contacter: '',
        contacterTel: '',
        remark: '',
        workingProvince: '',
        workingCity: '',
        workingCounty: '',
        remarkStatus: '',
        isReInvitation: '',
        score: {
          1: 0,
          2: 0,
          3: 0,
          4: 0,
          5: 0,
          6: 0
        },
        interviewRemark: '',
        interviewResult: '',
        isRetest: ''
      },
      cityOptions: [],
      receivedDetail: {}, // 简历投递详情
      DialogFormTitle: '',
      dialogFormStatus: ''
    }
  },
  computed: {
    ...mapGetters(['userInfo', 'compInfo']),
    // 全选状态
    checkedAll: {
      set(val) {
        this.studentResumeList.forEach(item => (item.ischecked = val))
      },
      get() {
        // return (
        //   this.resumeList.filter(item => item.ischecked).length === this.resumeList.length
        // )
        const checkedCount = this.studentResumeList.filter(item => item.ischecked).length
        if (checkedCount === 0) {
          // 没有选中任何项
          return false
        } else if (checkedCount === this.studentResumeList.length) {
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
      return !this.checkedAll && this.studentResumeList.some(item => item.ischecked)
    },

    calcScore() {
      this.interviewInvitationForm.score
      let totalScore = 0
      const totalItems = Object.keys(this.interviewInvitationForm.score).length
      for (const item in this.interviewInvitationForm.score) {
        totalScore += parseInt(this.interviewInvitationForm.score[item])
      }

      // 计算平均得分并返回结果
      const averageScore = totalScore / totalItems
      return averageScore.toFixed(2)
    }
  },
  mounted() {
    this.tabsList[this.checked - 1].active = true
    // this.$store.commit('settings/changeShowBreadCrumb', false)
    this.getPositionList()
    // this.getResumeList()
    this.getSalaryLists('学历')
    this.getPositionDropdownList()
    this.getSchoolList()
    this.getStuBasicInfo()
    this.getCityOptions()
  },
  methods: {
    // 折叠岗位区域
    toggle() {
      this.isCollapse = !this.isCollapse
    },
    // 切换tabs
    changeTab(item) {
      console.log()
      this.tabsList[this.checked - 1].active = false
      item.active = true
      this.checked = item.value
      const statusChange = {
        '1': () => { this.resumeStatus = '' }, // 获取全部状态的简历
        '2': () => { this.resumeStatus = 1 }, // 待审核/已投递
        '3': () => { this.resumeStatus = 9 }, // 已审核通过
        '4': () => {
          this.resumeStatus = 2
        }, // 待面试 2 8
        '5': () => { this.resumeStatus = 8 }, // 8已面试/待评价 14已面试/已评价
        '6': () => { this.resumeStatus = '' },
        '7': () => { this.resumeStatus = '' }, // 待签约3 31
        '8': () => { this.resumeStatus = 5 }, // 已签约
        '9': () => { this.resumeStatus = -6 } // 拒绝offer/已拒绝
      }
      statusChange[item.value]()

      console.log('sssss', this.resumeStatus)
      this.studentResumeList = [] // 清空旧列表
      this.getReceivedResumeList() // 重新请求获取已投递的简历
      if (item.value === 4) {
        this.getResumeReceivedDetail()
      }
    },

    getResumeReceivedDetail() {
      resumeReceivedDetail({
        receivedId: this.studentResumeInfo.currentResume.receivedId
      }).then(res => {
        console.log('获取简历投递详情', res)
        this.receivedDetail = res.data
        invitation({
          interviewId: res.data.inviteId
        }).then(res => {
          console.log('查询面试邀请', res)
        })
      })
    },

    // 邀约面试对话框选择省市区
    handleChange(val) {
      console.log(val, this.interviewInvitationForm)
      this.interviewInvitationForm.workingProvince = val[0]
      this.interviewInvitationForm.workingCity = val[1] || ''
      this.interviewInvitationForm.workingCounty = val[2] || ''
    },

    // 切换通过与未通过的开关
    switchClick(index, status) {
      this.filterStatus = index
      this.resumeStatus = status
      this.studentResumeList = [] // 清空旧列表
      this.getReceivedResumeList() // 重新请求获取已投递的简历
    },

    searchByName() {
      this.studentResumeList = []
      this.getReceivedResumeList()
    },

    async getCityOptions() {
      this.cityOptions = await getCityOptions()
    },

    // 设置标签
    handleClose(tag) {
      this.dynamicTags.splice(this.dynamicTags.indexOf(tag), 1)
    },

    showInput() {
      this.inputVisible = true
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus()
      })
    },

    // 按关键词搜索职位
    positionKeywordSearch() {
      this.positionList = []
      this.getPositionList()
    },

    handleInputConfirm() {
      const inputValue = this.inputValue
      if (inputValue) {
        this.dynamicTags.push(inputValue)
      }
      this.inputVisible = false
      this.inputValue = ''
    },

    // 获取企业职位列表
    getPositionList() {
      positionList({
        compId: this.compInfo.company.compId,
        instId: 'ROOT',
        name: this.positionSearch,
        pageIndex: this.positionPageIndex,
        pageSize: this.positionPageSize
      }).then(res => {
        console.log('获取第' + this.positionPageIndex + '页企业发布的职位列表', res)
        if (res.code === 200) {
          if (this.positionList.length === 0) {
            // this.selectedResumeItemUid = res.data.results.uid
            // this.selectedResumeItemInstid = res.data.results.instId
            this.studentResumeInfo.currentPosition = res.data.results[0]
            this.selectedPositionItemPid = res.data.results[0].pid
            this.getReceivedResumeList()
          }
          this.positionList = this.positionList.concat(res.data.results)
          this.positionTotal = res.data.total
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

    // 获取当前企业发布的职位列表
    getPositionDropdownList() {
      positionDropdownList({
        compId: this.compInfo.company.compId,
        orgId: '',
        status: ''
      }).then(res => {
        console.log('职位下拉列表', res)
        this.positionDropdownList = res.data
      })
    },

    // 获取学生简历列表
    // getResumeList() {
    //   resumeList({
    //     degree: this.resumeFrom.degree, // 学历
    //     getResumeDeliveryList: true,
    //     graduateDate: this.resumeFrom.graduationYear,
    //     // majorName: 'string',
    //     // name: '',
    //     pageIndex: this.pageIndex,
    //     pageSize: this.pageSize,
    //     score: this.resumeFrom.score,
    //     status: 1
    //   }).then(res => {
    //     console.log('学生简历列表', res)
    //     if (res.code === 200) {
    //       this.studentResumeList = res.data.results
    //       this.studentResumeList.map(item => {
    //         this.$set(item, 'ischecked', false)
    //       })
    //     }
    //   })
    // },

    // 某职位已投递的简历列表
    async getReceivedResumeList() {
      const params = {
        compId: this.compInfo.company.compId,
        degree: this.resumeFrom.degree,
        graduateDate: new Date(this.resumeFrom.graduationYear).getFullYear() || '',
        name: this.resumeSearch,
        pageIndex: this.resumeListPageIndex,
        pageSize: this.resumeListPageSize,
        pid: this.selectedPositionItemPid,
        status: this.resumeStatus
      }
      const res = await receivedResumeList(params)
      console.log('params', params)
      console.log('职位已投递的简历列表第' + this.resumeListPageIndex + '页', res)
      if (this.studentResumeList.length === 0) {
        if (res.data.results.length !== 0) {
          this.selectedResumeItemUid = res.data.results[0].uid
          this.selectedResumeItemInstid = res.data.results[0].instId
          this.selectedResumeItemReceivedId = res.data.results[0].receivedId
          this.studentResumeInfo.currentResume = res.data.results[0]
          this.getStuBasicInfo()
        }
      }
      this.studentResumeList = this.studentResumeList.concat(res.data.results)
      this.studentResumeListTotal = res.data.total
      this.studentResumeList.map(item => {
        this.$set(item, 'ischecked', false)
      })
    },

    // 表单筛选
    resumeFilter() {
      // this.getResumeList()
      this.studentResumeList = []
      this.getReceivedResumeList()
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
      this.studentResumeList = []
      this.getReceivedResumeList()
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
      console.log('sssssssssssssssss', arr)
      this.instOptions = arr
    },

    // 设置招聘标签
    setRecruitTags() {
      this.showSetRecruitDialog = true
    },

    closeSetRecruitDialog() {
      this.showSetRecruitDialog = false
      console.log('关闭-设置招聘标签对话框')
    },
    confirmSetRecruitDialog() {
      this.showSetRecruitDialog = false
      console.log('确认-设置招聘标签对话框')
    },

    // 获取学生基本信息
    async getStuBasicInfo() {
      const res = await stuBasicInfo({
        // instId: this.selectedResumeItemInstid,
        instId: 'INST20230104104700123456',
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
        // instId: this.selectedResumeItemInstid,
        instId: 'INST20230104104700123456',
        uid: this.selectedResumeItemUid
      })
      console.log('学生校内经历', res)
      this.studentResumeInfo.campExpeList = res.data
    },

    // 获取学生实习经历
    async getStuInternExpeList() {
      const res = await stuInternExpeList({
        // instId: this.selectedResumeItemInstid,
        instId: 'INST20230104104700123456',
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

    // 职位列表触底加载
    handlePositionListScroll() {
      const container = this.$refs.positionListContainer
      if (container.scrollTop + container.clientHeight >= container.scrollHeight && this.positionList.length < this.positionTotal) {
        this.positionPageIndex += 1
        this.getPositionList()
      }
    },

    // 简历列表触底加载
    handleResumeListScroll() {
      const container = this.$refs.resumeListContainer
      if (container.scrollTop + container.clientHeight >= container.scrollHeight && this.studentResumeList.length < this.studentResumeListTotal) {
        this.resumeListPageIndex += 1
        this.getReceivedResumeList()
      }
    },

    positionItemClick(data) {
      // this.$refs.resumeListContainer.scrollTop = 0
      this.studentResumeInfo.currentPosition = data
      this.selectedPositionItemPid = data.pid
      console.log('pid', data.pid)
      this.studentResumeList = []
      this.getReceivedResumeList()
    },

    handleResumeClick(data) {
      console.log('uid', data.uid)
      this.studentResumeInfo.currentResume = data
      this.studentResumeList.forEach(item => {
        item.ischecked = false
        if (item.uid === data.uid) {
          item.ischecked = true
        }
      })
      this.selectedResumeItemUid = data.uid
      this.selectedResumeItemInstid = data.instId
      this.selectedResumeItemReceivedId = data.receivedId
      this.getStuBasicInfo()
    },

    // 简历批量审核
    async handleApproveResumeBatch(status) {
      const res = await approveResumeBatch({
        receivedId: '',
        receivedStatus: 0,
        remark: '',
        status: 0
      })
    },

    // 简历单个审核
    async handleApproveResume(status) {
      const res = await approveResume({
        receivedId: this.selectedResumeItemReceivedId,
        receivedStatus: 1,
        remark: '',
        // score: '',
        status: status // -1拒绝 1同意
      })
      console.log('简历审核', res)
      if (res.code === 200) {
        this.$message({
          message: '审核成功',
          type: 'success'
        })
        this.studentResumeList = []
        this.getReceivedResumeList()
      }
    },

    // 单个面试邀请
    forInterview() {
      // interview({
      //   contacter: '',
      //   contacterTel: '',
      //   interviewInvitation: '',
      //   interviewTime: '',
      //   interviewVenue: '',
      //   receivedId: '',
      //   remark: '',
      //   type: ''
      // }).then(res => {
      //   if (res.code === 200) {
      //     this.$message({
      //       message: '邀约面试成功',
      //       type: 'success'
      //     })
      //   }
      // })
    },

    // 批量面试邀约
    forInterviewBatch() {
      interviewBatch({}).then(res => {
        if (res.code === 200) {
          this.$message({
            message: '邀约面试成功',
            type: 'success'
          })
        }
      })
    },

    // 取消填写面试邀约表单
    cancelIntervieInvitationDialog() {
      this.$refs.interviewInvitationFormRef.resetFields()
      console.log('xxxxxxxxxxxx', this.interviewInvitationForm)
      console.log('ssssssssssss', this.studentResumeInfo)
      this.showInterviewInvitationDialog = false
    },

    // 确认提交面试邀约表单
    confirmIntervieInvitationDialog() {
      if (this.dialogFormStatus === 1) {
        console.log('xxxxxxxxxxxx', this.interviewInvitationForm)
        interview({
          contacter: this.interviewInvitationForm.contacter,
          contacterTel: this.interviewInvitationForm.contacterTel,
          // interviewInvitation: '',
          interviewTime: Date.parse(this.interviewInvitationForm.dateTime) || '',
          interviewVenue: '',
          receivedId: this.studentResumeInfo.currentResume.receivedId,
          remark: this.interviewInvitationForm.remark,
          type: this.interviewInvitationForm.interviewType
        }).then(res => {
          if (res.code === 200) {
            this.$message({
              message: '邀约面试成功',
              type: 'success'
            })
          }
          // 重新发起请求，获取更新的数据
          this.studentResumeList = []
          this.getReceivedResumeList()
        })
        this.showInterviewInvitationDialog = false
        this.$refs.interviewInvitationFormRef.resetFields()
      } else if (this.dialogFormStatus === 2) {
        this.getInterviewRemark()
      }
    },

    openDialogForm(title, status) {
      this.dialogFormStatus = status
      this.showInterviewInvitationDialog = true
      this.DialogFormTitle = title
    },

    getInvitation() {
      console.log('xxxxxxxxxxxxx', this.studentResumeInfo.currentResume.inviteId)
      invitation({
        // interviewId: 'IV20230610145607435335'
        interviewId: this.studentResumeInfo.currentResume.inviteId
      }).then(res => {
        console.log('查询面试邀请', res)
      })
    },

    async getInterviewRemark() {
      const score = `态度：${this.interviewInvitationForm.score[3]}分,形象：${this.interviewInvitationForm.score[2]}分,智力：${this.interviewInvitationForm.score[4]}分,谈吐：${this.interviewInvitationForm.score[2]}分,性格：${this.interviewInvitationForm.score[5]}分,能力：${this.interviewInvitationForm.score[6]}分`
      const res = await interviewRemark({
        interviewId: this.studentResumeInfo.currentResume.inviteId,
        receivedId: this.studentResumeInfo.currentResume.receivedId,
        remark: '',
        score: score,
        status: this.interviewInvitationForm.interviewResult // 3通过，-2不通过，-5没参加面试
      })
      if (res.code === 200) {
        this.$message({
          message: '面试评价成功',
          type: 'success'
        })
        this.showInterviewInvitationDialog = false
        this.$refs.interviewInvitationFormRef.resetFields()
        // 重新发起请求，获取更新的数据
        this.studentResumeList = []
        this.getReceivedResumeList()
      }
    }

  }
}
</script>

<style scoped lang="scss">
.resumeFilterFrom ::v-deep .el-input {
  width: 160px;
}
.recruitContainer {
  display: flex;
  .recruit-l {
    width: 358px;
    // height: 102vh;
    // height: 100%;
    background-color: #fff;
    padding: 16px 24px;
    // margin-left: -12px;
    border-left: 1px solid #f8f9fa;
    border-right: 1px solid #f8f9fa;
    position: relative;
    top: -12px;
    bottom: -60px;
    left: -12px;
    margin-bottom: -48px;
    &.position-collapse {
      width: 51px;
      // height: 76vh;
      padding: 20px 8px;
      border-left: 1px solid #f8f9fa;
      border-right: 1px solid #f8f9fa;
      // border: 1px solid red;
      // margin-left: 0px;
      &.positions-top {
        display: none;
        color: #ff7171;
      }
    }
    .collapse-title {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      // font-size: 20px;
      & div:nth-child(2) {
        font-size: 14px;
        margin-top: 12px;
      }
    }
    .position-section {
      // width: 358px;
      // background-color: #ccc;
      .positions-top {
        font-size: 14px;
        .title {
          display: flex;
          justify-content: space-between;
          align-items: center;
          margin-bottom: 16px;
          .title-l {
            display: flex;
            & div:first-child {
              font-weight: 600;
            }
            & div:nth-child(2) {
              color: #999;
              margin-left: 7px;
            }
          }
          .title-r {
            font-size: 16px;
            span {
              font-size: 14px;
              margin-right: 8px;
            }
          }
        }
        .position-search {
          margin-bottom: 16px;
        }
      }
      .position-list {
        overflow-y: auto;
        // height: 940px;
        height: 640px;
        .positionItem {
          margin-bottom: 8px;
        }
      }
    }
  }
  .recruit-r {
    flex: 1;
    // height: calc(100vh - 96px);
    // background-color: #fff;
    .filter-section {
      .filter-section-top {
        padding: 24px;
        padding-bottom: 0;
        background-color: #fff;
        .tabs {
          height: 40px;
          display: flex;
          align-items: center;
          // background-color: #fff;
          font-size: 14px;
          font-weight: 500;
          color: #333333;
          // padding: 24px;
          border-bottom: 2px solid #f4f4f4;
          div {
            margin-right: 24px;
          }
          & div:last-child {
            margin-right: 0;
          }
          .active {
            color: #30b0bf;
            // border-bottom: 2px solid #30b0bf;
            &::after {
              content: '';
              position: absolute;
              bottom: -10px;
              left: 0;
              width: 100%;
              height: 2px;
              background: #30b0bf;
            }
          }
          .isSelect {
            position: relative;
            &::before {
              content: '';
              width: 10px;
              height: 10px;
              // background: #ff7171;
              border: 1px solid #ffffff;
              border-radius: 6px;
              position: absolute;
              top: -4px;
              right: -6px;
            }
          }
        }
        .search {
          margin-top: 24px;
          // padding: 24px;
          // background-color: #fff;
        }
      }
      .filter-main {
        display: flex;
        margin-top: 12px;
        height: calc(100vh - 60px);
        .main-left {
          width: 390px;
          background-color: #fff;
          padding: 24px;
          height: 100%;
          position: relative;
          .resume-search {
            // display: flex;
            .filter-status {
              // margin-top: 8px;
              font-size: 14px;
              height: 32px;
              display: flex;
              justify-content: center;
              span {
                cursor: pointer;
                color: #999;
                padding: 6px 10px;
                display: flex;
                justify-content: center;
                align-items: center;
                border-radius: 0px 2px 2px 0px;
                border: 1px solid #cccccc;
              }
              .filterStatusSelected {
                font-size: 14px;
                font-family: PingFangSC-Medium, PingFang SC;
                font-weight: 500;
                color: #30b0bf;
                border: 1px solid #30b0bf;
                background: #eaf7f9;
              }
            }
          }
          .checkAll {
            display: flex;
            padding: 0 16px;
            align-items: center;
            justify-content: space-between;
            height: 64px;
            position: absolute;
            z-index: 9;
            bottom: 0;
            left: 0;
            right: 0;
            background-color: #fff;
            border-top: 1px solid #f4f4f4;
          }
          .resume-list {
            height: 700px;
            // margin-bottom: 100px;
            overflow-y: auto;
            margin: 12px 0 40px;
            // background-color: red;
          }
        }
        .main-right {
          flex: 1;
          margin-left: 16px;
          background-color: #fff;
          padding: 24px;
          height: 100%;
          position: relative;
          // margin-bottom: 40px;
          .interview {
            padding: 16px 24px;
            background-image: url('~@/assets/schoolRecruit/interview-bg.png');
            background-size: cover;
            color: #30b0bf;
            margin-bottom: 12px;
            .interview-item {
              img {
                width: 16px;
                // height: 16px;
                margin-right: 4px;
              }
              div {
                font-size: 14px;
              }
            }
          }
          .mark-btns {
            display: flex;
            align-items: center;
            height: 64px;
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 0 24px;
            background-color: #fff;
            .review {
              position: absolute;
              background: #F8FAFA;
              top: -80px;
              width: 100%;
              height: 80px;
              padding: 8px;
              box-shadow: 0px 2px 8px 0px rgba(0,0,0,0.05)
            }
          }
        }
      }
    }
  }

  .el-tag + .el-tag {
    margin-left: 10px;
  }
  .button-new-tag {
    margin-left: 10px;
    height: 32px;
    line-height: 30px;
    padding-top: 0;
    padding-bottom: 0;
  }
  .input-new-tag {
    width: 90px;
    margin-left: 10px;
    vertical-align: bottom;
  }

  .recruit-studentInfo >>> .info-bottom >>> .pane-content {
    height: 330px !important;
  }
}

.setTagDialog {
  .dialog-container {
    .select-tags {
      height: 232px;
      border-top: 1px solid #F4F4F4;
      border-bottom: 1px solid #F4F4F4;
      .content-l {
        flex: 3;
        // width: 127px;
        display: flex;
        justify-content: center;
        padding: 16px;
        background: #F8F9FA;
        .cate-tags {
          padding: 8px 24px;
          font-size: 12px;
          font-family: PingFangSC-Medium, PingFang SC;
          font-weight: 500;
          color: #333333;
          line-height: 17px;
          background: #FFFFFF;
          border-radius: 4px;
          margin-bottom: 8px;
        }
      }
      .content-r {
        display: flex;
        flex-wrap: wrap;
        flex: 7;
        padding: 16px;
        .tagsListItem {
          // margin-bottom: 8px;
          width: 80px;
          height: 32px;
          background: #30B0BF;
          border-radius: 4px;
          font-size: 14px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: #FFFFFF;
          line-height: 20px;
          line-height: 32px;
          text-align: center;
          margin-right: 12px;
        }
      }
    }
    .showTags {
      margin-top: 12px;
      & div:first-child {
        font-size: 12px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: #999999;
        line-height: 17px;
      }
    }
  }
}

.interviewInvitationDialog {
  & ::v-deep .el-input {
    width: 300px;
  }
  & ::v-deep .el-form-item {
    margin-bottom: 8px;
  }
  .container {
    .resume-info {
      margin-bottom: 16px;
      .resume-l {
        img {
          width: 40px;
          height: 40px;
          border-radius: 50%;
          margin-right: 8px;
        }
        div div:first-child {
          font-size: 16px;
          font-family: PingFangSC-Medium, PingFang SC;
          font-weight: 500;
          color: #333333;
          line-height: 22px;
        }
        div div:last-child {
          font-size: 14px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: #999999;
          line-height: 20px;
        }
      }
    }
    .position-info {
      height: 78px;
      background: #F8F9FA;
      border-radius: 4px;
      border: 1px solid #F4F4F4;
      padding: 16px;
      margin-bottom: 20px;
      .position-l {
        & div:nth-child(1) {
          font-size: 16px;
          font-family: PingFangSC-Medium, PingFang SC;
          font-weight: 500;
          color: #333333;
          line-height: 22px;
          margin-bottom: 6px;
        }
        & div:nth-child(2) {
          font-size: 14px;
          font-family: PingFangSC-Regular, PingFang SC;
          font-weight: 400;
          color: #999999;
          line-height: 20px;
        }
      }
      .position-r {
        font-size: 14px;
        font-family: Gilroy-SemiBold, Gilroy;
        font-weight: 600;
        color: #999;
        line-height: 17px;
        & span {
          color: #30B0BF;
        }
      }
    }
    .interviewRate {
      & ::v-deep .el-form-item__content {
        margin-left: 0 !important;
        line-height: 20px;
      }
      .rate {
        background-color: #eaf7f8;
        // padding: 12px;
        height: 132px;
        border-radius: 4px;
        margin-bottom: 8px;
      }
      .el-rate {
        transform: scale(0.8);
      }
      .rete-title {
        height: 40px;
        font-size: 14px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: #333333;
        line-height: 20px;
        border-bottom: 1px solid #E3EDEF;
        padding: 0 16px;
        line-height: 40px;;
        & span {
          color: #FF8282 ;
        }
      }
      .rate-main {
        padding: 16px;
        &>div:nth-child(1) {
          margin-bottom: 16px;
        }
      }
    }

  }
}
</style>

// 应聘管理列表 需要添加的数据
// /org/resume/received/list/v2

// evaluationSelf 求职者的自我评价
// expCity 学生期望工作城市
// expectIndustry 求职者意向行业标签
// expectMaxSalary	期望薪资范围上限
// expectMinSalary	期望薪资范围下限
// expectPosition 期望职位
// expectSalary 期望薪资
// graduateDate 学生毕业年月
// headImg	头像
// majorName 学生的专业名称
// receivedId 简历投递Id
// skillTips 求职者个人标签
// talentTips 招聘者给这个职位的求职者打的标签

// 还需要增加一个字段: instId
