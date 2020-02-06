<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-form ref="addFormRef" :model="addForm" label-width="120px">
          <!--:model作用：可以使得el-form针对表单的全部信息进行收集，固定属性-->
          <el-form-item label="学科：">
            <el-select v-model="addForm.subjectID">
              <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="目录：">
            <el-select v-model="addForm.catalogID">
              <el-option
                v-for="item in catalogIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业：">
            <el-select v-model="addForm.enterpriseID">
              <el-option
                v-for="item in enterpriseIDList"
                :key="item.id"
                :label="item.shortName"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="城市：">
            <el-select v-model="addForm.province" @change="addForm.city=''">
              <el-option v-for="(item,k) in provinces()" :key="k" :label="item" :value="item"></el-option>
            </el-select>
            <el-select v-model="addForm.city">
              <el-option
                v-for="(item,k) in citys(addForm.province)"
                :key="k"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向：">
            <el-select v-model="addForm.direction">
              <el-option v-for="item in directionList" :key="item" :value="item" :label="item"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="题型：">
            <el-radio-group v-model="addForm.questionType">
              <el-radio
                v-for="item in questionTypeList"
                :key="item.value"
                :label=" item.value+'' "
              >{{item.label}}</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="难度：">
            <el-radio-group v-model="addForm.difficulty">
              <el-radio
                v-for="item in difficultyList"
                :key="item.value"
                :label=" item.value+'' "
              >{{item.label}}</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import {
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants.js'

import { list as companysList } from '@/api/hmmm/companys' // 企业
import { simple as subjectsSimple } from '@/api/hmmm/subjects' // 学科
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 二级目录
import { provinces, citys } from '@/api/hmmm/citys' // 城市和区县
// 常量(方向)
import { direction as directionList } from '@/api/hmmm/constants'
export default {
  name: 'QuestionsNew',
  data() {
    return {
      difficultyList, // 难度 简易成员赋值
      questionTypeList, // 题型 (简易成员赋值)
      enterpriseIDList: [], // 企业
      subjectIDList: [], // 学科
      catalogIDList: [], // 二级目录
      directionList, // 方向(简易成员赋值)
      // 添加试题 表单数据对象
      addForm: {
        // 如下表单字段名称来自yapi数据接口
        difficulty: '1', // 默认 难度 第一个项目被选中(要求是字符串)
        questionType: '1', // 默认“单选” 题型 项目被选中(要求是字符串)
        subjectID: '', // 学科
        catalogID: '', // 二级目录
        enterpriseID: '', // 企业
        city: '', // 区县
        province: '', // 城市
        direction: '' // 方向
      }
    }
  },
  created() {
    this.getEnterpriseIDList() // 获得企业
    this.getSubjectIDList() // 学科
    this.getCatalogIDList() // 二级目录
  },
  methods: {
    provinces, // 获得城市的方法(简易成员赋值)
    citys, // 获得地区的方法(简易成员赋值)
    // 获得 企业 列表信息
    async getEnterpriseIDList() {
      var result = await companysList()
      this.enterpriseIDList = result.data.items
    },
    // 学科 列表信息
    async getSubjectIDList() {
      var result = await subjectsSimple()
      this.subjectIDList = result.data
    },
    // 目录 列表信息
    async getCatalogIDList() {
      var result = await directorysSimple()
      this.catalogIDList = result.data
    }
  }
}
</script>

<style scoped>
</style>
