<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <el-row>
          <el-col>
            <el-button type="primary" size="mini" @click="$router.push('/questions/new')">{{ $t('question.newadd') }}</el-button>
            <el-button type="danger" size="mini">{{ $t('question.manyadd') }}</el-button>
          </el-col>
        </el-row>
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
            <el-select
              v-model="searchForm.province"
              placeholder="选城市"
              @change="getCitys(searchForm.province)"
              style="width:90px"
            >
              <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
            </el-select>
            <el-select v-model="searchForm.city" placeholder="选地区" style="width:90px">
              <el-option v-for="item in cityList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
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
        <el-table :data="questionList" style="width:100%">
          <el-table-column label="序号" type="index"></el-table-column>
          <el-table-column label="试题编号" prop="number"></el-table-column>
          <el-table-column label="学科" prop="subject"></el-table-column>
          <el-table-column label="题型" :formatter="questionTypeFMT" prop="questionType"></el-table-column>
          <el-table-column label="题干" prop="question"></el-table-column>
          <el-table-column label="录入时间" prop="addDate" width="170">
            <span slot-scope="stData">{{stData.row.addDate | parseTimeByString('{y}-{m}-{d}')}}</span>
          </el-table-column>
          <el-table-column label="难度" prop="difficulty" :formatter="difficultyFMT"></el-table-column>
          <el-table-column label="录入人" prop="creator"></el-table-column>
          <el-table-column label="操作" width="200">
            <template slot-scope="stData">
              <a href="#">预览</a>
              <a href="#">修改</a>
              <a href="#" @click.prevent="del(stData.row)">删除</a>
              <a href="#">加入精选</a>
            </template>
          </el-table-column>
        </el-table>
        <!-- 试题分页 -->
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="searchForm.page"
          :page-sizes="[3, 5, 10, 20]"
          :page-size="searchForm.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="tot"
        ></el-pagination>
      </el-card>
    </div>
  </div>
</template>

<script>
import { remove } from '@/api/hmmm/questions.js' // 删除基础试题api
import { list } from '@/api/hmmm/questions' // 基础题库相关api导入
import { provinces, citys } from '@/api/hmmm/citys' // 获取 省份/城市 信息方法导入
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
      questionList: [], // 基础题库列表信息
      cityList: [], // 区县信息
      catalogIDList: [], // 二级目录
      creatorIDList: [], // 录入人
      tagsList: [], // 标签
      // 定义各个搜索表单域的数据展示成员
      subjectIDList: [],
      difficultyList, // 简易成员赋值(difficultyList:difficultyList)
      questionTypeList, // 简易成员赋值
      tot: 0, // 数据总条数

      // 定义搜索数据对象
      searchForm: {
        page: 1, // 默认获取第1页数据
        pagesize: 3, // 默认每页获得3条数据
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
  // 制作watch监听器，对searchForm表单对象进行深度监听，发现搜索表单数据由变化，就重新获得试题列表信息
  watch: {
    // 搜索表单深度监听
    searchForm: {
      handler: function(newV, oldV) {
        // 任何条件改变都重新获得数据
        this.getQuestionList()
      },
      deep: true
    }
  },
  created() {
    // 获得基础题库列表信息
    this.getQuestionList()
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
    // 每页条数变化回调处理
    handleSizeChange(val) {
      // val：变化后的每页条数
      // 表单成员接收
      this.searchForm.pagesize = val
    },
    // 当前页码变化的回调处理
    handleCurrentChange(val) {
      // val: 变化后的当前页码
      // 表单成员接收
      this.searchForm.page = val
    },
    // 删除试题
    // question : 被删除试题的整条记录对象
    del(question) {
      // 确认框
      this.$confirm('确认要删除该记录么?', '删除', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          // 调用remove的api方法，实现删除
          let result = await remove(question)
          // console.log(result)
          this.$message.success('删除成功')
          // 数据刷新(旧的就不显示了)
          this.getQuestionList()
        })
        .catch(() => {})
    },
    // 对"难度"进行二次修饰
    difficultyFMT(row, column, cellValue, index) {
      return this.difficultyList[cellValue - 1].label
    },
    // 对"题型"数据进行二次修饰
    // row: 代表每条记录信息(stData.row.xx)
    // column:代表列的信息(一般不使用)
    // cellValue: 当前正在处理的目标列的内容(与row.questionType一致)
    questionTypeFMT(row, column, cellValue, index) {
      // console.log(index)
      // return 返回的信息会去覆盖当前的column列的内容
      return this.questionTypeList[cellValue - 1].label
    },
    // 获得基础题库列表信息
    async getQuestionList() {
      var result = await list(this.searchForm)
      // 把获得好的题库列表信息富裕给questionList
      this.questionList = result.data.items
      // 获得数据总条数并赋予给searchForm.tot成员
      this.tot = result.data.counts
    },
    // 获得 城市 信息
    // 这个pname形参就代表被选中的省份信息
    getCitys(pname) {
      this.searchForm.city = '' // 清除之前选取好的城市
      this.cityList = citys(pname)
    },
    // 获得 省份 信息，简易成员赋值
    provinces, // provinces:provinces
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

.el-table {
  margin-top: 20px;
}
.el-pagination{
  margin-top:15px;
}
</style>
