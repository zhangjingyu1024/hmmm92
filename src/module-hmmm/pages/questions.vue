<template>
  <div class="dashboard-container">
    <div class="app-container">
      
      <el-row>
        <el-col :span="6">
          学科：
          <el-select v-model="searchForm.subjectID">
            <el-option v-for="item in subjectIDList" :key="item.value" :value="item.value" :label="item.label">
            </el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          难度：
          <el-select v-model="searchForm.difficulty">
            <el-option v-for="item in difficultyList" :key="item.value" :value="item.value" :label="item.label">
            </el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          试题类型：
          <el-select v-model="searchForm.questionType" style="width:140px;">
            <el-option v-for="item in questionTypeList" :key="item.value" :value="item.value" :label="item.label">
            </el-option>
          </el-select>
        </el-col>
      </el-row>
      
    </div>
  </div>
</template>

<script>
// 把api数据接口相关方法导入进来
import { simple } from '@/api/hmmm/subjects' // 学科
// as给导入的成员设置别名
import {
  difficulty as difficultyList, 
  questionType as questionTypeList} 
  from '@/api/hmmm/constants' // 常量数据
export default {
  name: 'QuestionsList',
  data() {
      return {
      // 定义各个搜索表单域的数据展示成员
      subjectIDList: [],
      difficultyList, // 简易成员赋值(difficultyList:difficultyList)
      questionTypeList,// 简易成员赋值

      // 定义搜索数据对象
      searchForm: {
        subjectID: '',
        difficulty: '',
        questionType: ''
      }
  }
},
created(){
  // 学科
  this.getSubjectIDList()
},
methods: {
  // 获得学科下拉列表数据
  async getSubjectIDList(){
    var rst = await simple()
    // 把获得到的学科信息赋予给 subjectIDList成员 
    this.subjectIDList = rst.data
  }
},
}
</script>

<style scoped>
</style>
