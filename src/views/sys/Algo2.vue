<template>
  <div>
    <div>
      <h1 class="word-v-middle">XX问题求解</h1>
    </div>

    <span>算法选择</span>
    <p>     </p>
    <el-select style="width:20%" v-model="value" @change="changeparam" placeholder="请选择">
      <el-option
        v-for="item in options"
        :key="item.value"
        :label="item.label"
        :value="item.value"
        
      >
      </el-option>
    </el-select>

    <!-- <p>{{ value }}</p> -->

    
    <p>参数填写</p>
    <el-divider></el-divider>
    
    <el-input v-if="value==='选项1'" size="small" v-model="gaparam.param1" placeholder="请输入内容" style="width: 10%" @change="changeparam"></el-input>
    <el-input v-if="value==='选项1'" size="small" v-model="gaparam.param2" placeholder="请输入内容" style="width: 10%" @change="changeparam"></el-input>
    
    <p>{{gaparam.param1}}</p>
    <p>{{gaparam.param2}}</p>

    <el-input v-if="value==='选项2'"  size="small" v-model="auparam.param1" placeholder="请输入内容" style="width: 10%" @change="changeparam"></el-input>
    <el-input v-if="value==='选项2'"  size="small" v-model="auparam.param2" placeholder="请输入内容" style="width: 10%" @change="changeparam"></el-input>
    <el-input v-if="value==='选项2'"  size="small" v-model="auparam.param3" placeholder="请输入内容" style="width: 10%" @change="changeparam" ></el-input>
    <p>{{auparam.param1}}</p>
    <p>{{auparam.param2}}</p>
    <p>{{auparam.param3}}</p>


    <p>上传模板</p>
    <el-divider></el-divider>


    <!-- 计算部分 -->
    <el-form label-width="120px">
      <el-form-item>
        <el-tag type="info">算法模板</el-tag>
        <el-tag>
          <i class="el-icon-download" />

          <a
            :href="'https://onlineedu-jth.oss-cn-beijing.aliyuncs.com/TSN-CPLEX_Template.xlsx'"
            >点击下载模版</a
          >
        </el-tag>
      </el-form-item>

      <!-- <el-form-item label="选择Excel">
        <el-upload
          class="upload-demo"
          ref="upload"
          :action="BASE_API + `/algo/algo1/SaveAndComputealgo1/${this.token}`"
          :on-success="fileUploadSuccess"
          :on-error="fileUploadError"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :file-list="fileList"
          :disabled="importBtnDisabled"
          :limit="1"
          :auto-upload="false"
        >
          <el-button slot="trigger" size="small" type="primary"
            >选取本地文件</el-button
          >

          <el-button
            style="margin-left: 10px"
            size="small"
            type="success"
            @click="submitUpload"
            >上传并开始求解</el-button
          >
        </el-upload>
      </el-form-item> -->

      <el-form-item label="选择Excel">
        <el-upload
          class="upload-demo"
          ref="upload"
          :action="BASE_API + `/algo/algochoice/ChoiceAndCompute/${this.token}/${this.value}/${this.param}`"
          :on-success="fileUploadSuccess"
          :on-error="fileUploadError"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :file-list="fileList"
          :limit="1"
          :auto-upload="false"
        >
          <el-button slot="trigger" size="small" type="primary"
            >选取本地文件</el-button
          >
          <el-button
            style="margin-left: 10px"
            size="small"
            type="success"
            @click="submitUpload"
            >上传并开始求解</el-button
          >
        </el-upload>
      </el-form-item>




    </el-form>
    <!-- 显示部分 -->
    <el-table :data="tableData" height="300" border style="width: 40%">
      <el-table-column prop="mission" label="任务" width="180">
      </el-table-column>
      <el-table-column prop="server" label="服务器" >
      </el-table-column>
    </el-table>


    
  <el-table
    :data="allTableData"
    height="250"
    border
    style="width: 40%">
    <el-table-column
      prop="conclusion"
      label="结论"
      width="180">
    </el-table-column>
    <el-table-column
      prop="time"
      label="简介"
      >
    </el-table-column>
  </el-table>

  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      BASE_API: axios.defaults.baseURL,
      importBtnDisabled: false,
      fileList: [],
      tableData: [],
      allTableData:[],
      token: "",
      dateBegin: null,
      dateEnd: null,
      conclusion: [],
      param:"",
      gaparam:{
        param1: "1" ,
        param2: "2" ,
      },
      auparam:{
        param1:"3",
        param2:"4",
        param3:"5",
      },
      options: [
        {
          value: "选项1",
          label: "遗传算法",
        },
        {
          value: "选项2",
          label: "拍卖算法",
        },
      ],
      value: "选项1",
    };
  },
  created() {
    this.token = localStorage.getItem("token");
    this.changeparam();
  },
  methods: {
    changeparam(){
      if(this.value==="选项1"){
        this.param=JSON.stringify(this.gaparam)
      }
      if(this.value==="选项2"){
        this.param=JSON.stringify(this.auparam)
      }
      
    },
    open() {
      this.$message("正在计算，请稍后");
    },
    //点击按钮上传文件到接口里面
    submitUpload() {
        console.log(this.param);
        this.dateBegin = new Date();
        this.open();
        this.$refs.upload.submit();
    },


    //上传成功
    fileUploadSuccess(response,file) {
      this.importBtnDisabled = true;
      console.log(response);
      this.tableData = response.data.slice(0, -1);
      this.conclusion = response.data.slice(-1);
      this.dateEnd = new Date();
      let timeDiff = this.dateEnd - this.dateBegin;
      //总结
      // console.log(this.conclusion)
      this.allTableData = this.allTableData.concat(this.conclusion)
      // console.log(this.allTableData)

      //提示信息
      this.$message({
        type: "success",
        message: "计算结束,耗时" + timeDiff + "ms,请查看结果",
      });
      //重新准备上传
      file.status = 'ready'
      // this.$refs.upload.clearFiles(); //上传成功之后清除历史记录
    },
    //上传失败
    fileUploadError() {
      
      this.$message({
        type: "error",
        message: "上传失败",
      });

    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePreview(file) {
      console.log(file);
    },
  },
};
</script>

<style scoped>
.el-container {
  padding: 0;
  margin: 0;
  height: 100%;
}

.header-avatar {
  float: right;
  width: 210px;
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.el-dropdown-link {
  cursor: pointer;
}

.el-header {
  background-color: #17b3a3;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  line-height: 200px;
}

.el-main {
  color: #333;
  padding: 0;
}

.a {
  text-decoration: none;
}

.word-v-middle {
  margin-bottom: 0;
  font-size: 20px;
  min-height: 31px;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 31px;
  margin-top: 5px;
  color: #000000;
  white-space: normal;
}
.word-v-middle span {
  text-align: left;
  font-size: 10px;
  text-overflow: -o-ellipsis-lastline;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
</style>