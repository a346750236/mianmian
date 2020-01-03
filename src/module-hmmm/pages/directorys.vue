<template>
       
   <el-card class="directorys">
       <el-row>
       <el-button type="primary" @click="dialogVisble=true"><i class="el-icon-plus"></i> 新增项目</el-button>
       <el-button type="primary">返回学科</el-button>
     </el-row>
     <!-- 导航栏组件 -->
     <el-card class="directorys-nav"> 
     <el-row>
       <el-form type="flex" inline class="forma">
         <!-- 目录名称 -->
         <el-form-item label="目录名称">
          <el-col :span="2">
          </el-col>
            <el-col :span="16">
             <el-input v-model="searchForm.directoryName"></el-input>
          </el-col>
         </el-form-item>
         <!-- 状态 -->
         <el-form-item label="状态">
          <el-col :span="2">
          </el-col>
            <el-col :span="16">
            <el-select v-model="searchForm.state"> 
              <el-option 
              v-for="item  in status" 
              :label="item.label"
              :value="item.value" 
              :key="item.vlaue" 
              ></el-option>
            </el-select>
          </el-col>
         </el-form-item>
         <!-- 清除和搜索按钮 -->
        <el-button @click="clear">清除</el-button>
        <el-button type="primary" @click="search">搜索</el-button>
       </el-form>
     </el-row>
     </el-card>
     <!-- 内容区域 -->
     <el-table :data="list" style="width: 100%">  
     <el-table-column prop="id" label="序号">
     </el-table-column>
     <el-table-column prop="directoryName" label="目录名称" >
       </el-table-column>
     <el-table-column  prop="creatorID" label="创建者">
       </el-table-column>
     <el-table-column  prop="addDate" label="创建日期">
       <!-- 作用域插槽 -->
       <template slot-scope="obj">{{obj.row.addDate | parseTimeByString}}</template>
       </el-table-column>
     <el-table-column  prop="totals" label="面试题数量">
        </el-table-column>
     <el-table-column  prop="state" label="状态" :formatter="formatterStatus">
       </el-table-column>
     <el-table-column  prop="address" label="操作">
       <!-- 操作 -->
       <!-- 作用域插槽 -->
        <template slot-scope="obj">
        <el-button type="text" size="small" @click="modify(obj.row.id)">修改</el-button>
        <el-button type="text" size="small" @click="changestate(obj.row)">
          {{obj.row.state === 1 ? '禁用': '启用'}}
        </el-button>
        <el-button type="text" size="small" @click="deleremove(obj.row.id)">删除</el-button>
        </template>
       </el-table-column>
     </el-table>
     <!-- 获取数据 -->
     <!-- {{list}} -->
   <!-- 分页 -->
   <el-pagination
    :total="page.total"
    :current-page="page.currentPage"
    :page-size="page.pageSize" 
     type="flex"
     align="right" style="height:60px" 
     class="directorys-paging" background 
     layout="prev, pager, next"></el-pagination>
     <!-- 放置一个弹层 -->
     <el-dialog :show-close="false" :visible=" dialogVisble">
       <el-form ref="refform" :model="publichForm" :rules="rules" label-width="80px">
         <el-form-item label="目录名称" prop="directoryName">
           <el-input v-model="publichForm.directoryName" style="width:50%"></el-input>
         </el-form-item>
         <el-form-item label="学科" prop="subjectID">
           <el-select v-model="publichForm.subjectID">
             <el-option v-for="item in subjectsimple" :value="item.value" :key="item.value" :label="item.label" ></el-option>
           </el-select>
         </el-form-item>
       </el-form>
       <el-row slot="footer" type="flex" justify="end">
         <el-button @click="ok" type="primary">确定</el-button>
         <el-button @click="cancel">取消</el-button>
       </el-row>
     </el-dialog>
   </el-card>
</template>

<script>
import {list, remove,removeState,add,detail} from "../../api/hmmm/directorys"
import {simple} from "../../api/hmmm/directorys"
import {status} from "../../api/hmmm/constants"
export default {
  name: 'DirectorysList',
  data() {
    return {
      dialogVisble:false,
      searchForm:{
      directoryName: '',//目录名称
      state: ''//状态
      },
      status,
      list:[],
      page: {
        total: 0,
        pageSize: 9,
        currentPage: 1
      },
      subjectsimple:[],
      publichForm: {
        subjectID: null,
        directoryName: ""
      },
      rules: {
        subjectID: [{required: true, message: "学科不能为空",trigger: "blur"}],
        directoryName: [{required: true, message: "目录不能为空" ,trigger: "blur"}],
      }
    }
  },
  methods: {
    //修改
   async modify (id) {
     let result= await detail({id});
     this.publichForm = result.data;
     this.dialogVisble =true
    },
    ok () {
      this.$refs.refform.validate(async isok =>{
    if (isok) {
    await  add(this.publichForm);
    this.publichForm ={
      subjectID: null,
        directoryName: ""
    },
      this.dialogVisble=false;
      this.getDirectory()
    }
      });
    
    },
    cancel () {
      this.publichForm ={
      subjectID: null,
        directoryName: ""
    },
      this.dialogVisble=false
    },
    //修改状态
   async changestate (row) {
    await this.$confirm("确定要改状态码");
    removeState({id: row.id, state: row.state ===1 ? 0 : 1});
     this.$message({type: "success", message: "修改成功"});
     this.getDirectory()
    },
    //删除
   async deleremove (id) {
     await this.$confirm("确定要删除吗");
     await remove({id});
     this.$message({type: "success", message: "删除成功"});
     this.getDirectory(this.searchForm)

    },
    //搜索
    search () {
      this.page.currentPage = 1;
      this.getDirectory(this.searchForm);
    },
    //清除
    clear () {
      this.searchForm = {
        directoryName: "",
        state: null
      }
    },
    //状态显示
    formatterStatus (row, column, cellValue, index ) {
      let result = this.status.filter(item => item.value ===cellValue);
    return  result.length ? result[0].label : "未知状态" ;
    },
    //获取目录数据
   async  getDirectory (data) {
    let result= await list({
        page: this.page.currentPage,
        pageSize: this.page.pageSize,
        ...data
      });
       this.list = result.data.items;
       this.page.total = result.data.counts;
    },
   async getSimple () {
      let result = await simple();
      this. subjectsimple = result.data;
    }
  },
  created () {
    this.getDirectory();
    this.getSimple()
  }
}
</script>

<style lang="scss"  scoped>
.directorys{
  background-color: #e6f4ff;
  i{
   padding: 0px 3px;
  }
  .directorys-nav{
    margin-top: 15px;
  }
  .directorys-paging{
    align-content: center;
  }
}
</style>
