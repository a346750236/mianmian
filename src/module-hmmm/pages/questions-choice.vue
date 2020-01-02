<template>
  <!-- 卡片 -->
  <el-card>
    <el-row>
      <!-- 头部两个按钮 -->
      <el-button type="primary">新增试题</el-button>
      <el-button type="primary">批量导入</el-button>
    </el-row>
    <!-- 分割线 -->
    <el-divider></el-divider>
    <!-- 下拉框区域 -->
    <el-row>
      <!-- 学科 -->
      <el-form :model="formData" ref="myForm" inline label-width="80px">
        <el-form-item label="学科" prop="subjectID">
          <el-select v-model="formData.subjectID">
            <el-option
              v-for="item in subjects"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="状态" prop="status">
          <el-select v-model="formData.status">
            <el-option
              v-for="item in status"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度" prop="difficulty">
          <el-select v-model="formData.difficulty">
            <el-option
              v-for="item in difficulty"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="试题类型" prop="questionType">
          <el-select v-model="formData.questionType">
            <el-option
              v-for="item in questionType"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签" prop="tags">
          <el-select v-model="formData.tags">
            <el-option
              v-for="item in tags"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="关键字" prop="keyword">
          <el-input v-model="formData.keyword"></el-input>
        </el-form-item>
        <el-form-item label="题目备注" prop="remarks">
          <el-input v-model="formData.remarks"></el-input>
        </el-form-item>
        <el-form-item label="企业简称" prop="shortName">
          <el-input v-model="formData.shortName"></el-input>
        </el-form-item>

        <el-form-item label="录入人" prop="creatorID">
          <el-select v-model="formData.creatorID">
            <el-option
              v-for="(item) in users"
              :key="item.id"
              :value="item.id"
              :label="item.username"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录" prop="catalogID">
          <el-select v-model="formData.catalogID">
            <el-option
              v-for="(item) in directioy"
              :key="item.id"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市" prop="province">
          <el-select v-model="formData.province">
            <el-option v-for="(item,index) in provinces" :key="index" :label="item" :value="item"></el-option>
            {{provinces}}
          </el-select>
        </el-form-item>
        <el-form-item label="区域" prop="city">
          <el-select v-model="formData.city">
            <el-option v-for="(item,index) in myCitys" :key="index" :label="item" :value="item"></el-option>
            <!-- {{myCitys}} -->
          </el-select>
        </el-form-item>
        <el-form-item label="方向" prop="direction">
          <el-select v-model="formData.direction">
            <el-option v-for="(item,index) in direction" :key="index" :value="item" :label="item"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </el-row>
    <!-- 双按钮 -->
    <el-row type="flex" justify="center" style="margin-top:20px">
      <el-button @click="Eliminate">清除</el-button>
      <el-button @click="cearch" type="primary">搜索</el-button>
    </el-row>
    <!-- 切换table表格 -->
    <el-row style="margin:20px 0">
      <el-radio-group v-model="queryStatus">
        <el-radio-button label="all">全部</el-radio-button>
        <el-radio-button label="wait">待发布</el-radio-button>
        <el-radio-button label="publish">已发布</el-radio-button>
      </el-radio-group>
    </el-row>
    <!-- table表格 -->
    <el-table :data="currentList" style="width: 100%">
      <el-table-column prop="id" label="序号"></el-table-column>
      <el-table-column prop="number" label="试题编号"></el-table-column>
      <el-table-column :formatter="subjectFormatter" prop="subjectID" label="学科"></el-table-column>
      <el-table-column :formatter="questionFormatter" prop="questionType" label="题型"></el-table-column>
      <el-table-column prop="question" label="题干"></el-table-column>
      <el-table-column prop="addDate" label="录入时间">
        <template slot-scope="obj">
          {{
          obj.row.addDate | parseTimeByString
          }}
        </template>
      </el-table-column>
      <el-table-column prop="creator" label="录入入"></el-table-column>
      <el-table-column prop="difficulty" label="难度"></el-table-column>
      <el-table-column :formatter="chkFormatter" prop="chkState" label="审核状态"></el-table-column>
      <el-table-column prop="chkRemarks" label="审核意见"></el-table-column>
      <el-table-column prop="chkUser" label="审核人"></el-table-column>
      <el-table-column prop="publishState" label="发布状态">
        <template
          slot-scope="obj"
        >{{ obj.row.publishState === 0 ? '待发布' :obj.row.publishState === 1 ? '已发布':'已下架' }}</template>
      </el-table-column>
      <el-table-column label="操作" width="180">
        <template slot-scope="obj">
          <el-button type="text" size="small">审核</el-button>
          <el-button type="text" size="small">预览</el-button>
          <el-button type="text" size="small" @click="UpDown(obj.row)">
            {{
            (obj.row.publishState === 0 || obj.row.publishState ===1) ? '上架' : '下架'
            }}
          </el-button>
          <el-button type="text" size="small" @click="modify(obj.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="Deletes(obj.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-row type="flex" justify="end" style="height:60px" align="middle">
      <el-pagination
        background
        :page-size="page.pageSize"
        :current-page="page.currentPage"
        layout="total, prev, pager, next"
        :total="page.total"
        @current-change="change"
      ></el-pagination>
    </el-row>
  </el-card>
</template>

<script>
// choice 精选题库列表    remove 删除   choicePublish  精选题库上下架
import {
  choice as QLchoice,
  remove,
  choicePublish
} from "../../api/hmmm/questions";
// 下拉框
// questionType 题型  direction 方向   difficulty 难度  status 状态
import {
  questionType,
  direction,
  difficulty,
  status
} from "../../api/hmmm/constants";
import { simple as TagList } from "../../api/hmmm/tags"; //  标签列表
import { simple as subjectList } from "../../api/hmmm/subjects"; // 学科简单列表
import { simple as UserList } from "../../api/base/users"; // 录入入
import { simple as directioy } from "../../api/hmmm/directorys"; // 目录简单列表
import { citys, provinces } from "../../api/hmmm/citys"; // // 所有城市
export default {
  data() {
    return {
      queryStatus: "all", //默认全部
      // 下拉框数据
      list: [], // 精选题库数据
      subjects: [], // 学科
      tags: [], // 标签
      direction, // 方向
      difficulty, // 难度
      questionType, //试题类型
      directioy, // 二级目录
      citys: [], // 区域
      users: [], // 录入入
      provinces: provinces(), // 城市
      // 状态
      status: [
        {
          value: 0,
          label: "待审核"
        },
        {
          value: 1,
          label: "通过"
        },
        {
          value: 2,
          label: "拒绝"
        }
      ],
      formData: {
        subjectID: null, // 学科
        difficulty: null, // 难度
        questionType: null, //试题类型
        tags: null, // 标签名称
        keyword: null, // 关键字
        remarks: null, //题目备注
        shortName: null, //企业简称
        creatorID: null, // 录入入
        direction: null, //  方向
        city: null, // 城市
        catalogID: null, // 目录
        province: null, // 所在省份
        chkState: null // 审核状态
      },
      // 分页数据
      page: {
        currentPage: 1, // 当前默认显示第一页
        total: 0, // 总条数
        pageSize: 10 // 每页显示10个
      }
    };
  },
  computed: {
    // 区域
    myCitys() {
      return citys(this.formData.province);
    },
    // 切换表单
    currentList() {
      if (this.queryStatus === "all") return this.list.map(item => item);
      else if (this.queryStatus === "wait")
        return this.list.filter(
          item => item.publishState === 0 || item.publishState === 2
        );
      else return this.list.filter(item => item.publishState === 1);
    }
  },
  methods: {
    // 转换学科
    subjectFormatter(row, column, cellValue) {
      let result = this.subjects.filter(item => item.value === cellValue);
      return result.length ? result[0].label : "-";
    },
    // 审核状态
    chkFormatter(row, column, cellValue) {
      return cellValue === 0 ? "待审核" : cellValue === 1 ? "通过" : "拒绝";
    },
    // 转换题型
    questionFormatter(row, column, cellValue) {
      let result = this.questionType.filter(item => item.value == cellValue);
      return result.length ? result[0].label : "未知";
    },
    // 清除数据
    Eliminate() {
      // 对整个表单进行重置，将所有字段值重置为初始值并移除校验结果
     this.$refs.myForm.resetFields()
    },
    // 分页数据
    change(newPage) {
      this.page.currentPage = newPage;
      // 刷新数据
      this.questionbank();
    },
    // 精选题库渲染
    async questionbank(data) {
      let result = await QLchoice(data);
      this.list = result.data.items; // 获取数据
      this.page.total = result.data.counts; // 总页数
    },
    // 删除数据
    async Deletes(id) {
      await this.$confirm("您确定要删除该数据吗?");
      await removeSc({ id });
      this.$message({
        type: "success",
        message: "删除成功"
      });
      //  精选题库渲染
      this.getCondition();
    },
    // 搜索
     cearch() {
      // 搜索完回到第一页
      this.page.currentPage = 1;
      this.getCondition();
    },
    // 上架
    async UpDown(row) {
      await this.$confirm("确定要进行此操作吗");
      await choicePublish({
        id: row.id,
        publish: row.publishState === 0 || row.publishState === 2 ? 1 : 0
      });
      this.getCondition();
      this.$message({ type: "success", message: "操作成功" });
    },
    // 填充数据刷新页面
    getCondition() {
      let params = {
        page: this.page.currentPage,
        pageSize: this.page.pageSize,
        ...this.downdData
      };
      this.questionbank(params);
    },
    // 获取学科数据
    async getSubject() {
      let result = await subjectList();
      this.subjects = result.data;
    },
    // 获取标签
    async getTags() {
      let result = await TagList();
      this.tags = result.data;
    },
    // 获取录入入
    async getUserList() {
      let result = await UserList();
      this.users = result.data;
    },
    // 获取目录
    async getDirectioy() {
      let result = await directioy();
      this.directioy = result.data;
    }
  },
  created() {
    // 精选题库渲染
    this.questionbank();
    // 学科
    this.getSubject();
    // 获取标签
    this.getTags();
    // 获取录入入
    this.getUserList();
    // 获取目录
    this.getDirectioy();
  }
};
</script>

<style scoped lang="scss">
// 1. 如果在此处 scoped 当前组件下生效样式
// 2. 在style中的所有选择器 编译的时候会自动带上属性选择器
// 3. .box{} ===> .box[data-v-2c9827a4]{} 交集选择器
// 4. 在当前组件下暴露的标签都会加上 data-v-2c9827a4 属性
// 5. 但是如果是组件，其他组件内部的标签是不会加上这个属性 意味写组件内部的样式是不会生效的
// 6. 其他组件的样式都不会生效
</style>
