<template>
  <div class="search">
    <div class="search-form">
      <el-form ref="resumeFrom" :model="resumeFrom" :inline="true">
        <el-form-item label="申请职位">
          <el-select v-model="resumeFrom.degree" placeholder="按申请职位">
            <el-option
              v-for="item in positionOptions"
              :key="item.name"
              :label="item.name"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="毕业年份">
          <el-select v-model="resumeFrom.degree" placeholder="按毕业年份">
            <el-option
              v-for="item in positionOptions"
              :key="item.name"
              :label="item.name"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="学历">
          <el-select v-model="resumeFrom.degree" placeholder="按学历">
            <el-option
              v-for="item in positionOptions"
              :key="item.name"
              :label="item.name"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="院校专业">
          <el-select v-model="resumeFrom.degree" placeholder="按院校专业">
            <el-option
              v-for="item in positionOptions"
              :key="item.name"
              :label="item.name"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="老师评级">
          <el-select v-model="resumeFrom.degree" placeholder="老师推荐等级">
            <el-option
              v-for="item in positionOptions"
              :key="item.name"
              :label="item.name"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
      </el-form>
    </div>

    <div class="btns">
      <el-button type="primary">筛选</el-button>
      <el-button type="primary" plain>重置</el-button>
    </div>
  </div>
</template>

<script>
export default {

}
</script>

<style lang="scss" scoped>
.search {
  display: flex;
}
</style>
