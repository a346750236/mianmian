<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form
        ref="form"
        :model="formData"
        :rules="rules"
        label-width="80px"
        style="margin-left:100px"
      >
        <el-form-item label="学科" prop="subjectID">
          <el-select v-model="formData.subjectID" placeholder="请选择" style="width:40%">
            <el-option
              v-for="item in form.subjectOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="目录" prop="catalogID">
          <el-select v-model="formData.catalogID" placeholder="请选择" style="width:40%">
            <el-option
              v-for="item in form.directoryOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="企业" prop="enterpriseID">
          <el-select v-model="formData.enterpriseID" placeholder="请选择" style="width:19%">
            <el-option
              v-for="item in form.companyOptions"
              :key="item.id"
              :label="item.shortName"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-row type="flex" justify="start">
          <el-col :span="6">
            <el-form-item label="城市" prop="province">
              <el-select v-model="formData.province" placeholder="请选择" style="margin-right:20px">
                <el-option
                  v-for="(item,index) in form.provinceOptions"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item prop="city">
              <el-select v-model="formData.city" placeholder="请选择" style="margin-left:-60px">
                <el-option
                  v-for="(item,index) in form.cityOptions"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-form-item label="方向" prop="direction">
          <el-select v-model="formData.direction" placeholder="请选择" style="width:40%">
            <el-option
              v-for="(item,index) in form.directionOptions"
              :key="index"
              :label="item"
              :value="item"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="题型" prop="questionType">
          <el-radio-group v-model="formData.questionType">
            <el-radio :label="1">单选</el-radio>
            <el-radio :label="2">多选</el-radio>
            <el-radio :label="3">简答</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="难度" prop="difficulty">
          <el-radio-group v-model="formData.difficulty">
            <el-radio :label="1">简单</el-radio>
            <el-radio :label="2">一般</el-radio>
            <el-radio :label="3">困难</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="题干" prop="question">
          <quill-editor
            v-model="formData.question"
            style="width:60%;height:300px;margin-bottom:100px"
          ></quill-editor>
        </el-form-item>
        <el-form-item
          v-if="formData.questionType === 1"
          label="选项(以下选中的选项为正确答案)"
          style="margin-top:50px;"
          prop="options"
        >
          <div v-for="(item,index) in formData.options" :key="index" style="margin-bottom:10px">
            <el-radio v-model="radio" :label="form.xxxoptions[index]"></el-radio>
            <el-input style="width:40%" v-model="formData.options[index].title"></el-input>
            <el-button style="margin-left:15px" @click="deleteOption(index)">
              <i class="el-icon-delete"></i>删除该选项
            </el-button>
          </div>
          <el-button
            type="primary"
            style="margin-top:10px;display:block"
            @click="createOptions"
          >增加选项与答案</el-button>
        </el-form-item>
        {{radio}}
        {{formData.options}}
        <el-form-item
          v-if="formData.questionType === 2"
          label="选项(以下选中的选项为正确答案)"
          style="margin-top:50px;"
          prop="options"
        >
          <div v-for="(item,index) in formData.options" :key="index" style="margin-bottom:10px">
            <el-checkbox v-model="item.isRight" :label="form.xxxoptions[index]"></el-checkbox>
            <el-input style="width:40%" v-model="formData.options[index].title"></el-input>
            <el-button style="margin-left:15px" @click="deleteOption(index)">
              <i class="el-icon-delete"></i>删除该选项
            </el-button>
          </div>
          <el-button
            type="primary"
            style="margin-top:10px;display:block"
            @click="createOptions"
          >增加选项与答案</el-button>
        </el-form-item>
        <el-form-item label="解析视频" prop="videoURl">
          <el-input style="width:40%" v-model="formData.videoURl"></el-input>
        </el-form-item>
        <el-form-item label="答案解析" prop="answer">
          <quill-editor
            v-model="formData.answer"
            style="width:60%;height:300px;margin-bottom:125px"
          ></quill-editor>
        </el-form-item>
        <el-form-item label="题目备注" prop="remarks">
          <el-input style="width:40%" v-model="formData.remarks"></el-input>
        </el-form-item>
        <el-form-item label="试题标签" prop="tags">
          <quill-editor v-model="formData.tags" style="width:60%;height:300px;margin-bottom:100px"></quill-editor>
        </el-form-item>
      </el-form>
      <el-row type="flex" align="middle" justify="center" style="margin-top:40px">
        <el-button type="primary" @click="submitForm('form')">提交</el-button>
        <el-button @click="resetForm('form')">清空</el-button>
      </el-row>
    </div>
  </div>
</template>

<script>
import { simple as subjectSimpleList } from "../../api/hmmm/subjects"; //学科列表
import { simple as directorysSimpleList } from "../../api/hmmm/directorys"; //目录列表
import { list as companysList } from "../../api/hmmm/companys"; //公司列表
import {
  provinces as cityList,
  citys as cityAreas
} from "../../api/hmmm/citys"; //省份(城市)/区域列表
import { direction } from "../../api/hmmm/constants"; //方向列表
import { quillEditor } from "vue-quill-editor"; //引入富文本组件
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";
import { add as addQuestion} from '../../api/hmmm/questions'//上传试题
export default {
  name: "QuestionsNew",
  components: {
    "quill-editor": quillEditor
  },
  data() {
    return {
      radio: null,
      checked: [],
      form: {
        subjectOptions: [],
        directoryOptions: [],
        companyOptions: [],
        provinceOptions: [],
        cityOptions: [],
        directionOptions: direction,
        xxxoptions: ["A", "B", "C", "D", "E", "F", "G", "H", "I"]
      },
      formData: {
        number: null, //试题编号
        subjectID: "", //学科
        catalogID: null, //目录
        enterpriseID: null, //企业
        city: "", //城市
        province: "", //省份
        direction: "", //方向
        questionType: null, //题型
        difficulty: null, //难度
        question: "", //题干
        options: [
          // {
          //   code: "", //代码
          //   title: "", //标题
          //   img: "", //图片URL
          //   isRight: "" //是否正确答案
          // }
        ], //选项
        videoURl: "", //解析视频
        answer: "", //答案解析
        remarks: "", //题目备注
        tags: "", //试题标签
        isPerfect: null //是否精选题
      },
      rules: {
        subjectID: [
          { required: true, message: "请选择科目类型", trigger: "blur" }
        ],
        catalogID: [{ required: true, message: "请选择目录", trigger: "blur" }],
        enterpriseID: [
          { required: true, message: "请选择企业", trigger: "blur" }
        ],
        city: [
          { required: true, message: "请选择城市(区域)", trigger: "blur" }
        ],
        province: [
          { required: true, message: "请选择省份(城市)", trigger: "blur" }
        ],
        direction: [{ required: true, message: "请选择方向", trigger: "blur" }],
        questionType: [
          { required: true, message: "请选择题型", trigger: "blur" }
        ],
        difficulty: [
          { required: true, message: "请选择难度", trigger: "blur" }
        ],
        question: [
          { required: true, message: "请输入题干内容" },
          {
            min: 10,
            max: 500,
            message: "题干长度在 10 到 500个字符",
            trigger: "blur"
          }
        ],
        videoURl: [
          { required: true, message: "请输入视频链接", trigger: "blur" }
        ],
        answer: [
          { required: true, message: "请输入答案解析", trigger: "blur" },
          {
            min: 10,
            max: 500,
            message: "题干长度在 10 到 500个字符",
            trigger: "blur"
          }
        ],
        remarks: [
          { required: true, message: "请输入题目备注", trigger: "blur" }
        ],
        tags: [
          { required: true, message: "请输入答案解析", trigger: "blur" },
          {
            min: 10,
            max: 500,
            message: "题干长度在 10 到 500个字符",
            trigger: "blur"
          }
        ]
      }
    };
  },
  methods: {
    getProvinces() {
      this.form.provinceOptions = cityList();
    },
    createOptions() {
      this.formData.options.push({
        code: this.form.xxxoptions[this.formData.options.length],
        title: "",
        img: "",
        isRight: false
      });
    },
    deleteOption(index) {
      this.$confirm("确定删除此选项吗").then(() =>
        this.formData.options.splice(index, 1)
      );
    },
    resetForm(formName) {
      this.$confirm("确定清空所有内容吗").then(() =>
        this.$refs[formName].resetFields()
      );
    },
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          addQuestion(this.formData)
          this.$router.push('../list')
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    }
  },
  watch: {
    "formData.province": {
      handler(val) {
        this.form.cityOptions = cityAreas(val);
        this.formData.city = this.form.cityOptions[0];
      }
    }
  },
  created() {
    subjectSimpleList().then(res => {
      this.form.subjectOptions = res.data;
    }); //获取学科列表
    directorysSimpleList().then(res => {
      this.form.directoryOptions = res.data;
    }); //获取目录列表
    companysList().then(res => {
      this.form.companyOptions = res.data.items;
    }); //获取公司列表
    this.getProvinces(); //获取省份/直辖市
  }
};
</script>

<style  scoped>
.app-container {
  background-color: #fff;
}
</style>