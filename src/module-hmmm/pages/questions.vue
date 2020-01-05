<template>
  <el-row class="question">
    <!-- 头部内容 -->
    <div class="header">
      <el-row>
        <el-button type="primary">新增试题</el-button>
        <el-button type="primary">批量导入</el-button>
      </el-row>
    </div>
    <!-- 主体内容 -->
    <div class="content">
      <!-- 上 选项下拉区域 -->
      <el-form ref="form" :model="formdata" label-width="80px" class="content-form">
        <el-form-item prop="subjectValue" label="学科">
          <el-select placeholder="请选择" v-model="formdata.subjectValue">
            <el-option
              v-for="item in subjectList"
              :key="item.id"
              :label="item.subjectName"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item prop="difficultyValue" label="难度">
          <el-select placeholder="请选择" v-model="formdata.difficultyValue">
            <el-option
              v-for="item in difficulty"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item prop="questionType" label="试题类型">
          <el-select placeholder="请选择" v-model="formdata.questionType">
            <el-option
              v-for="item in questionType"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item prop="tagsValue" label="标签">
          <el-select placeholder="请选择" v-model="formdata.tagsValue">
            <el-option
              v-for="item in tags"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item prop="provincesValue" label="城市">
          <el-select @change="getArea()" placeholder="请选择" v-model="formdata.provincesValue">
            <el-option v-for="(item,index) in provinces" :key="index" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item prop="cityValue" label="区域">
          <el-select placeholder="请选择" v-model="formdata.cityValue">
            <el-option v-for="(item,index) in citys" :key="index" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item prop="keywordValue" label="关键字">
          <el-input class="common-button" v-model="formdata.keywordValue"></el-input>
        </el-form-item>
        <el-form-item prop="remarkValue" label="题目备注">
          <el-input class="common-button" v-model="formdata.remarkValue"></el-input>
        </el-form-item>
        <el-form-item label="企业简介" prop="companyValue">
          <el-input class="common-button" v-model="formdata.companyValue"></el-input>
        </el-form-item>
        <el-form-item label="方向">
          <el-select placeholder="请选择" v-model="formdata.directionValue">
            <el-option v-for="(item,index) in direction" :key="index" :label="item" :value="index"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="录入人">
          <el-select placeholder="请选择" v-model="formdata.usersValue">
            <el-option v-for="item in user" :key="item.id" :label="item.username" :value="item.id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录">
          <el-select placeholder="请选择" v-model="formdata.simpletValue">
            <el-option
              v-for="item in directorys"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <el-row type="flex" justify="center" style="margin:20px 0">
        <el-button @click="clearData">清除</el-button>
        <el-button type="primary">搜索</el-button>
      </el-row>
      <!-- {{list}} -->
      <!-- 中 表格展示区域 -->
      <el-table :data="list" class="content-table">
        <el-table-column prop="id" label="序号" width="80"></el-table-column>
        <el-table-column prop="number" label="试题编号" width="120"></el-table-column>
        <el-table-column prop="subjectID" label="学科"></el-table-column>
        <el-table-column prop="questionType" label="题型" :formatter="formatType"></el-table-column>
        <el-table-column prop="question" label="题干" width="200"></el-table-column>
        <el-table-column prop="addDate" label="录入时间" :formatter="formatTime" width="180"></el-table-column>
        <el-table-column prop="difficulty" :formatter="formatDifficulty" label="难度"></el-table-column>
        <el-table-column prop="creator" label="录入人"></el-table-column>
        <el-table-column prop="operation" label="操作" width="200">
          <template slot-scope="obj">
            <el-button type="text" size="small" @click="previewId(obj.row.id)">预览</el-button>
            <el-button type="text" size="small" @click="modify(obj.row.id)">修改</el-button>
            <el-button type="text" size="small" @click="delData(obj.row.id)">删除</el-button>
            <el-button type="text" size="small" @click="handpick">加入精选</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 下 分页底部区域 -->
      <el-row type="flex" justify="end" align="middle" class="content-pagination">
        <span class="pagination__total">共 {{page.counts}} 条</span>
        <el-pagination
          background
          layout="prev, pager, next"
          :total="page.counts"
          :page-size="page.pageSize"
          :current-page="page.currentPage"
          @current-change="searchPage"
        ></el-pagination>
      </el-row>
    </div>
    <!-- 预览弹出框 -->
    <el-dialog title="题目预览" :visible="status" :before-close="closePreview">
      <el-row style="margin-bottom:20px">
        <el-col :span="6">题库:面试宝典题库</el-col>
        <el-col :span="6">学科:{{detailData.subjectID}}</el-col>
      </el-row>
      <el-row style="margin-bottom:20px">
        <el-col :span="6">题型:{{detailData.questionType}}</el-col>
        <el-col :span="6">编号:{{detailData.number}}</el-col>
        <el-col :span="6">难度:{{detailData.difficulty}}</el-col>
        <el-col :span="6">标签:{{detailData.tags}}</el-col>
      </el-row>
      <el-row style="margin-bottom:20px">
        <el-col :span="6">目录:{{computedDirectory}}</el-col>
        <el-col :span="6">企业:{{detailData.enterpriseID}}</el-col>
        <el-col :span="6">方向:{{detailData.direction}}</el-col>
        <el-col :span="6">城市:{{detailData.city}}</el-col>
      </el-row>
      <el-row slot="footer" type="flex" justify="end">
        <el-button type="primary" @click="closePreview">关闭</el-button>
      </el-row>
    </el-dialog>
    <!-- {{directorys}} -->
  </el-row>
</template>

<script>
import { list as questionList, detail, remove } from "../../api/hmmm/questions";
import { list as subjectList } from "../../api/hmmm/subjects";
import { simple as directorys } from "../../api/hmmm/directorys";
import { simple as tags } from "../../api/hmmm/tags";
import { simple as user } from "../../api/base/users";
import { provinces, citys } from "../../api/hmmm/citys";
import { difficulty, questionType, direction } from "../../api/hmmm/constants";
import { log } from "util";
export default {
  data() {
    return {
      status: false,
      list: [], //基础题库列表
      // 下拉内容
      formdata: {
        subjectValue: null, //学科下拉框value
        simpletValue: null, //二级目录下拉框value
        tagsValue: null, //标签下拉框
        difficultyValue: null, //难度下拉框选中值
        questionType: null, //试题类型下拉框选中值
        directionValue: null, //方向下拉框选中值
        usersValue: null, //录入人选中值
        provincesValue: "", //城市下拉选中值
        cityValue: "", //地区下拉选中值
        keywordValue: "", //关键字的值
        remarkValue: "", //题目备注的值
        companyValue: "" //企业简介值
      },
      subjectList: [], //学科
      directorys: [], //二级目录
      tags: [], //标签
      difficulty, //难度
      questionType, //题型
      direction, //方向
      user: [], //录入人
      provinces: provinces(), //城市
      citys: [], //地区
      page: {
        counts: 0, //总记录数
        pageSize: 4, //每页显示条数
        // pages: 0, //总页数
        currentPage: 1 //当前页码  默认1开始
      },
      // 预览
      detailData: {
        id: null, //编号(题库)
        number: null, //试题编号
        difficulty: null, //难度
        questionType: null, //题型
        subjectID: null, //学科
        city: null, //城市
        tags: "", //试题标签
        direction: null, //方向
        enterpriseID: null, //企业
        catalogID: null //目录
      }
    };
  },
  methods: {
    // 预览
    async previewId(id) {
      let res = await detail({ id });
      this.detailData = res.data;
      this.status = true;
    },
    closePreview() {
      this.status = false;
    },
    //修改
    modify(id) {
      this.$router.push({ path: "/questions/new", query: { id } });
    },
    // 刪除
    async delData(id) {
      await this.$confirm("是否删除此文章");
      await remove({ id });
      this.$message({ type: "success", message: "删除成功" });
      this.changecondition();
    },
    // 加入精选
    async handpick(id) {
      await this.$confirm("是否加入精选");
      this.$message({ type: "success", message: "加入成功" });
    },
    // 先获取一个完整的数组,再将颠倒过后数组给包含params的方法,让他从颠倒的数组里取  没法传页数
    // async getQuestionArray() {
    //   let res = await questionList();
    //   let arr = res.data.items.reverse();
    // },
    async getQuestionList(params) {
      // this.getQuestionArray();
      let res = await questionList(params);
      this.list = res.data.items.reverse();
      this.page.counts = res.data.counts; //总记录条数
    },
    getsubjectList() {
      subjectList().then(res => {
        this.subjectList = res.data.items;
      });
    },
    async getsimpleList() {
      let res = await directorys();
      this.directorys = res.data;
    },
    async getTagsList() {
      let res = await tags();
      this.tags = res.data;
    },
    async getUserList() {
      let res = await user();
      this.user = res.data;
    },
    // 改变页码 传入改变的条件
    changecondition() {
      let params = {
        page: this.page.currentPage,
        pagesize: this.page.pageSize
      };
      this.getQuestionList(params);
    },
    // 点击分页 从当前点击页查询
    searchPage(newPage) {
      this.page.currentPage = newPage;
      this.changecondition();
    },
    // 获取地区
    getArea() {
      let area = this.formdata.provincesValue;
      this.citys = citys(area);
      this.formdata.cityValue = "";
    },
    //清除输入的内容 重置为初始值
    clearData() {
      this.$refs.form.resetFields();
    },
    //格式化题型
    formatType(row, column, cellValue, index) {
      let res = this.questionType.filter(item => item.value == cellValue);
      return res.length ? res[0].label : "未知";
    },
    // 格式化难度
    formatDifficulty(row, column, cellValue, index) {
      let res = this.difficulty.filter(
        item => item.value == cellValue
        // 第一种
        // if (item.value == cellValue) {
        //   return item;
        // }
        // return
        // 第二种
        // return item.value == cellValue
      );
      // console.log(res)
      return res.length ? res[0].label : "未知";
    },
    // 格式化时间
    formatTime(row, column, cellValue, index) {
      let utc_datetime = cellValue;
      var T_pos = utc_datetime.indexOf("T");
      var Z_pos = utc_datetime.indexOf("Z");
      var year_month_day = utc_datetime.substr(0, T_pos);
      var hour_minute_second = utc_datetime.substr(
        T_pos + 1,
        Z_pos - T_pos - 1
      );
      var new_datetime = year_month_day + " " + hour_minute_second; // 2017-03-31 08:02:06

      // 处理成为时间戳
      timestamp = new Date(Date.parse(new_datetime));
      timestamp = timestamp.getTime();
      timestamp = timestamp / 1000;

      // 增加8个小时，北京时间比utc时间多八个时区
      var timestamp = timestamp + 8 * 60 * 60;
      // 时间戳转为时间
      //  debugger;
      var beijing_datetime = new Date(parseInt(timestamp) * 1000)
        .toLocaleString()

        .replace(/年|月/g, "-")
        .replace(/日/g, " ");
      return beijing_datetime;
    }
  },
  computed: {
    computedDirectory() {
      let result =
        this.directorys &&
        this.directorys.filter(item => item.value == this.detailData.catalogID);
      return result && result.length ? result[0].label : "未知";
      console.log(result);
    }
  },
  created() {
    this.changecondition(); //基础题库列表
    this.getsubjectList(); //学科简单列表
    this.getsimpleList(); //目录简单列表(二级目录)
    this.getTagsList(); //标签简单列表
    this.getUserList(); //录入人简单列表
  }
};
</script>

<style lang="scss">
.el-card__body {
  padding: 0;
}
.question {
  .header {
    padding: 18px 20px;
    border-bottom: 1px solid #ebeef5;
  }
  .content {
    padding: 20px;
    .content-form {
      display: flex;
      flex-wrap: wrap;
      .el-form-item__content {
        margin-right: 20px;
      }
      .common-button {
        width: 222px;
      }
    }
    .content-pagination {
      height: 60px;
      .pagination__total {
        margin-right: 10px;
        font-weight: 400;
        font-size: 13px;
        color: #606266;
      }
    }
  }
}
</style>
