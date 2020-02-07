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
          <el-form-item label="题干：">
            <el-input type="textarea" v-model="addForm.question"></el-input>
          </el-form-item>
          <el-form-item label="答案：">
            <el-input type="textarea" v-model="addForm.answer"></el-input>
          </el-form-item>
          <el-form-item label="备注：">
            <el-input type="textarea" v-model="addForm.remarks"></el-input>
          </el-form-item>
          <el-form-item label="标签：">
            <el-input type="text" v-model="addForm.tags"></el-input>
          </el-form-item>

          <el-form-item label="选项：" v-if="anyShow">
            <template v-if="radioShow">
            <el-radio v-model="singleSelect" :label="0">
              A:
              <el-input type="text" v-model="addForm.options[0]['title']"></el-input>
            </el-radio>
            <br />
            <el-radio v-model="singleSelect" :label="1">
              B:
              <el-input type="text" v-model="addForm.options[1]['title']"></el-input>
            </el-radio>
            <br />
            <el-radio v-model="singleSelect" :label="2">
              C:
              <el-input type="text" v-model="addForm.options[2]['title']"></el-input>
            </el-radio>
            <br />
            <el-radio v-model="singleSelect" :label="3">
              D:
              <el-input type="text" v-model="addForm.options[3]['title']"></el-input>
            </el-radio>
            </template>

            <template v-else>
                          <el-checkbox v-model="addForm.options[0].isRight">
              A:
              <el-input type="text" v-model="addForm.options[0]['title']"></el-input>
            </el-checkbox>
            <br />
            <el-checkbox v-model="addForm.options[1].isRight" >
              B:
              <el-input type="text" v-model="addForm.options[1]['title']"></el-input>
            </el-checkbox>
            <br />
            <el-checkbox v-model="addForm.options[2].isRight" >
              C:
              <el-input type="text" v-model="addForm.options[2]['title']"></el-input>
            </el-checkbox>
            <br />
            <el-checkbox v-model="addForm.options[3].isRight">
              D:
              <el-input type="text" v-model="addForm.options[3]['title']"></el-input>
            </el-checkbox>
            </template>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="addQuestion()">提交</el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import { add } from '@/api/hmmm/questions' // 试题
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
      radioShow: true, // 默认显示单选项目
      anyShow: true, // 单选或多选默认显示一个
      // 感知被被选中的项目的值，是中间成员，需要通过watch转变为isRight
      singleSelect: '',
      difficultyList, // 难度 简易成员赋值
      questionTypeList, // 题型 (简易成员赋值)
      enterpriseIDList: [], // 企业
      subjectIDList: [], // 学科
      catalogIDList: [], // 二级目录
      directionList, // 方向(简易成员赋值)
      // 添加试题 表单数据对象
      addForm: {
        // 如下表单字段名称来自yapi数据接口
        question: '', // 题干
        answer: '', // 答案
        remarks: '', // 备注
        tags: '', // 标签
        videoURL: 'http://www.xxx.com', // 解析视频
        difficulty: '1', // 默认 难度 第一个项目被选中(要求是字符串)
        questionType: '1', // 默认“单选” 题型 项目被选中(要求是字符串)
        subjectID: '', // 学科
        catalogID: '', // 二级目录
        enterpriseID: '', // 企业
        city: '', // 区县
        province: '', // 城市
        direction: '', // 方向
        // 选项表单数据对象部分
        options: [
          // {code: '编号ABCD', title: '当前项目文字答案',
          //   img: '当前项目图片答案', isRight: boolean表明当前项目是否是答案},
          { code: 'A', title: '', img: '', isRight: false },
          { code: 'B', title: '', img: '', isRight: false },
          { code: 'C', title: '', img: '', isRight: false },
          { code: 'D', title: '', img: '', isRight: false }
        ]
      }
    }
  },
  created() {
    this.getEnterpriseIDList() // 获得企业
    this.getSubjectIDList() // 学科
    this.getCatalogIDList() // 二级目录
  },
  watch: {
    // 监听题型，进而切换显示 选项表单域
    'addForm.questionType': function(newV) {
      if (newV === '1') {
        // 单选
        this.anyShow = true
        this.radioShow = true
      } else if (newV === '2') {
        // 多选
        this.anyShow = true
        this.radioShow = false
      } else {
        // 简单
        this.anyShow = false
      }
    },
    singleSelect(newV, oldV) {
      // 设置当前单选按钮中情况,即isRight的值发送变化
      // 1. 先让全部项目处于false不选中状态
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      // 2. 当前选中项目的isRight为ture
      this.addForm.options[newV].isRight = true
    }
  },
  methods: {
    provinces, // 获得城市的方法(简易成员赋值)
    citys, // 获得地区的方法(简易成员赋值)
    // 添加试题
    async addQuestion() {
      // 调用api
      // await除了获得具体返回结果，还有一个作用
      // 等待添加完成再向后执行，可以保证添加的数据会在列表中展示
      await add(this.addForm)
      // console.log(result)  // 有返回新试题的id信息
      // 提示成功信息
      this.$message.success('添加试题成功！')
      // 页面跳转到列表去
      this.$router.push('/questions/list')
    },
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
