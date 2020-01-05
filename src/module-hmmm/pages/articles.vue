<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card>
        <el-form>
          <el-form-item>
            <!-- <el-button @click="$router.push({name:'articles-add'})" type="primary">新增面试技巧</el-button> -->
            <!-- <el-button type="primary">新增面试技巧</el-button> -->
            <!-- <el-popover placement="bottom-start" width="400" trigger="click">
              <el-form>
                <el-form-item label="学科名称">
                  <el-input class="keyWord"></el-input>
                </el-form-item>
              </el-form>
              <el-button slot="reference" type="primary">新增面试技巧</el-button>
            </el-popover>-->
            <el-button type="primary" @click="dialogTableVisible = true">新增面试技巧</el-button>

            <el-dialog title="新增面试技巧" :visible.sync="dialogTableVisible">
              <el-form :model="pubArtticle">
                <el-form-item label="学科名称">
                  <el-input class="keyWord" v-model="pubArtticle.title"></el-input>
                </el-form-item>
                <el-form-item label="内容">
                  <el-input class="keyWord" v-model="pubArtticle.articleBody"></el-input>
                </el-form-item>
                <el-form-item label="视频地址">
                  <el-input class="keyWord" v-model="pubArtticle.videoURL"></el-input>
                </el-form-item>
                <el-form-item class="pub_btn">
                  <el-button type="primary" @click="publishArticle">确定</el-button>
                  <el-button @click="dialogTableVisible = false">取消</el-button>
                </el-form-item>
                
              </el-form>
            </el-dialog>
          </el-form-item>

          <el-form-item label="关键字">
            <el-input class="keyWord"></el-input>
            <el-button>清除</el-button>
            <el-button type="primary">搜索</el-button>
          </el-form-item>
        </el-form>
        <el-table stripe :data="articleList" style="width:100%">
          <el-table-column label="序号" width="210" porp="id"></el-table-column>
          <el-table-column label="标题" width="210" prop="title"></el-table-column>
          <el-table-column label="阅读数" width="210" prop="reads"></el-table-column>
          <el-table-column label="状态" width="210" prop="state"></el-table-column>
          <el-table-column label="录入人" width="210" prop="creator"></el-table-column>
          <el-table-column label="操作">
            <template slot-scope="obj">
              <el-button type="text">预览</el-button>
              <el-button type="text" @click="updataArticl(obj.row)">修改</el-button>
              <el-button type="text" @click="delArticle(obj.row)">删除</el-button>
              <el-button type="text" @click="changeType(obj.row)">禁用</el-button>
            </template>
            
          </el-table-column>
        </el-table>
        <div style="display:flex;justify-content: flex-end;margin-top:20px">
          <span>共3条</span>
          <el-pagination
            style="display:inline-block"
            background
            layout="prev, pager, next"
            :total="1"
          ></el-pagination>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list,add,remove,state,detail } from "../../api/hmmm/articles";
export default {
  name: "ArticlesList",
  data() {
    return {
      articleList: [{
        id: "",
        title: "",
        reads: "",
        state: "",
        creator: ""
      }],
      dialogTableVisible:false,
      pubArtticle:{
        title:"",
        articleBody:"",
        videoURL:""
      }
    };
  },
  methods: {
    getArticle(){
      list().then(res => {
        this.dialogTableVisible=false;
        this.articleList = res.data.items;
      });
    },
    publishArticle(){
      add(this.pubArtticle).then(()=>{
        this.getArticle()
      })
    },
    async delArticle(row){
      console.log(row.id);
      // let id = row.id
      
      await this.$confirm('确定要删除吗？')
      await remove(row).then(()=>{
        this.getArticle()
      })
    },
    async changeType(row){
      await this.$confirm('确定要修改状态吗？')
      await state(row).then(()=>{
        this.getArticle()
      })
    },
    updataArticl(row){
      this.dialogTableVisible=true;
      detail(row).then(res=>{
        this.articleList.title=res.title;
        this.articleList.articleBody=res.articleBody;
        this.articleList.videoURL=res.videoURL;
      })
      
    }
  },
  created() {
    this.getArticle()
  }
};
</script>

<style scoped>
.keyWord {
  width: 200px;
  margin-right: 10px;
}
.pub_btn{
  display: flex;
  justify-content: flex-end;
}
</style>
