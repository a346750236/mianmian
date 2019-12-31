<template>
  <el-card class="question">
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
      <el-form label-width="80px" class="content-form">
        <el-form-item label="学科">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="1"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="试题类型">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="3"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="4"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="5"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="区域">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="6"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="关键字">
          <el-input class="common-button"></el-input>
        </el-form-item>
        <el-form-item label="题目备注">
          <el-input class="common-button"></el-input>
        </el-form-item>
        <el-form-item label="企业简介">
          <el-input class="common-button"></el-input>
        </el-form-item>
        <el-form-item label="方向">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="7"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="录入人">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="8"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录">
          <el-select placeholder="请选择" v-model="value">
            <el-option label="静态数据" value="9"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <el-row type="flex" justify="center" style="margin:20px 0">
        <el-button>清除</el-button>
        <el-button type="primary">搜索</el-button>
      </el-row>
      <!-- 中 表格展示区域 -->
      <el-table :data="tableData" class="content-table">
        <el-table-column prop="order" label="序号" width="80"></el-table-column>
        <el-table-column prop="number" label="试题编号" width="120"></el-table-column>
        <el-table-column prop="subject" label="学科"></el-table-column>
        <el-table-column prop="type" label="题型"></el-table-column>
        <el-table-column prop="stem" label="题干" width="200"></el-table-column>
        <el-table-column prop="date" label="录入时间" width="180"></el-table-column>
        <el-table-column prop="difficulty" label="难度"></el-table-column>
        <el-table-column prop="person" label="录入人"></el-table-column>
        <el-table-column prop="operation" label="操作" width="200">
          <template>
            <el-button type="text" size="small">预览</el-button>
            <el-button type="text" size="small">修改</el-button>
            <el-button type="text" size="small">删除</el-button>
            <el-button type="text" size="small">加入精选</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 下 分页底部区域 -->
      <el-row type="flex" justify="end" align="middle" class="content-pagination">
        <span class="pagination__total">共 1 条</span>
        <el-pagination background layout="prev, pager, next" :total="1"></el-pagination>
      </el-row>
    </div>
  </el-card>
</template>

<script>
export default {
  data() {
    return {
      tableData: [
        {
          order: "1",
          number: "测试试题编号",
          subject: "测试学科",
          type: "测试题型",
          stem: "测试题干",
          date: "测试录入时间",
          difficulty: "测试难度",
          person: "测试录入人",
          operation: "测试操作"
        }
      ],
      value:'1'
    };
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
