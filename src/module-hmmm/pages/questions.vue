<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row>
        <el-col :span="6" :gutter="20">
          学科：
          <el-select v-model="searchForm.subjectID">
            <el-option
              v-for="item in subjectIDList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          难度：
          <el-select v-model="searchForm.difficulty">
            <el-option
              v-for="item in difficultyList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          试题类型：
          <el-select v-model="searchForm.questionType" style="width:140px;">
            <el-option
              v-for="item in questionTypeList"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          标签：
          <el-select v-model="searchForm.tags" style="width:140px;">
            <el-option
              v-for="item in tagsList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="6" :gutter="20">
          城市：
          <el-select v-model="searchForm.province" placeholder="选城市" style="width:90px"></el-select>
          <el-select v-model="searchForm.city" placeholder="选地区" style="width:90px"></el-select>
        </el-col>
        <el-col :span="6">
          关键字：
          <el-input type="text" v-model="searchForm.keyword" class="wd"></el-input>
        </el-col>
        <el-col :span="6">
          试题备注：
          <el-input type="text" v-model="searchForm.remarks" class="wd"></el-input>
        </el-col>
        <el-col :span="6">
          企业简称：
          <el-input type="text" v-model="searchForm.shortName" class="wd"></el-input>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="6" :gutter="20">
          方向：
          <el-select placeholder="请选择" v-model="searchForm.direction" style="width:135px"></el-select>
        </el-col>
        <el-col :span="6">
          录入人：
          <el-select placeholder="请选择" v-model="searchForm.creatorID" style="width:135px">
            <el-option
              v-for="item in creatorIDList"
              :key="item.id"
              :label="item.username"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          二级目录：
          <el-select placeholder="请选择" v-model="searchForm.catalogID" style="width:135px">
            <el-option
              v-for="item in catalogIDList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-col>
        <el-col :span="6">
          <el-button size="mini">清除</el-button>
          <el-button type="primary" size="mini">搜索</el-button>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 获取二级目录信息方法导入
import { simple as usersSimple } from '@/api/base/users' // 获取录入人信息方法导入
// 获取标签信息方法导入
import { simple as tagsSimple } from '@/api/hmmm/tags'
// 把api数据接口相关方法导入进来
import { simple } from '@/api/hmmm/subjects' // 学科
// as给导入的成员设置别名
import {
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants' // 常量数据
export default {
  name: 'QuestionsList',
  data() {
    return {
      catalogIDList: [], // 二级目录
      creatorIDList: [], // 录入人
      tagsList: [], // 标签
      // 定义各个搜索表单域的数据展示成员
      subjectIDList: [],
      difficultyList, // 简易成员赋值(difficultyList:difficultyList)
      questionTypeList, // 简易成员赋值

      // 定义搜索数据对象
      searchForm: {
        subjectID: '', // 科学
        difficulty: '', // 难度
        questionType: '', // 试题类型
        tags: '', // 标签
        province: '', // 省份
        city: '', // 城市
        keyword: '', // 关键字
        remarks: '', // 备注
        shortname: '', // 企业简称
        direction: '', // 方向
        creatorID: '', // 录入人
        catalogID: '' // 二级目录
      }
    }
  },
  created() {
    // 获得 二级目录 信息
    this.getCatalogIDList()
    // 获得录入人信息
    this.getCreatorIDList()
    // 学科
    this.getSubjectIDList()
    // 标签
    this.getTagsList()
  },
  methods: {
    // 获得二级目录下拉列表数据
    async getCatalogIDList() {
      var result = await directorysSimple()
      // console.log(result)
      this.catalogIDList = result.data
    },
    // 获得录入人下拉列表数据
    async getCreatorIDList() {
      var result = await usersSimple()
      this.creatorIDList = result.data
    },
    // 获得 标签 列表数据
    async getTagsList() {
      var result = await tagsSimple() // promise对象 变为dt 了
      this.tagsList = result.data
      // console.log(result)
    },
    // 获得学科下拉列表数据
    async getSubjectIDList() {
      var rst = await simple()
      // 把获得到的学科信息赋予给 subjectIDList成员
      this.subjectIDList = rst.data
    }
  }
}
</script>

<style scoped>
.wd {
  width: 170px;
}
.el-row {
  margin-bottom: 10px;
}
</style>
