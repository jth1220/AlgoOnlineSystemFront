<template>
  <div>
    <div>
      <h1 class="word-v-middle">拍卖算法求解</h1>
    </div>
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

      <el-form-item label="选择Excel">
        
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
      </el-form-item>
    </el-form>

    <el-table :data="tableData" height="300" border style="width: 40%">
      <el-table-column prop="mission" label="任务" width="180">
      </el-table-column>
      <el-table-column prop="server" label="服务器" >
      </el-table-column>
    </el-table>
    <el-table :data="conclusion" height="200" border style="width: 20%">
      <el-table-column prop="time" label="时间" width="100">
      </el-table-column>
      <el-table-column prop="conclusion" label="结论">
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
      tableData: [],
      importBtnDisabled: false,
      fileList: [],
      tableData:[],
      token:'',
      dateBegin:null,
      dateEnd:null,
      conclusion:[],
    };
  },
  created() {
    this.token=localStorage.getItem('token');
  },
  methods: {
    open() {
      this.$message('正在计算，请稍后');
    },
    //点击按钮上传文件到接口里面
    submitUpload() {
      this.dateBegin=new Date();
      this.loading = true;
      // js: document.getElementById("upload").submit()
      this.open()
      this.$refs.upload.submit()
      
    },
    //上传成功
    fileUploadSuccess(response) {
      this.importBtnDisabled = true;
      console.log(response)
      this.tableData=response.data.slice(0,-1)
      this.conclusion= response.data.slice(-1)
      console.log('***************'+response.data)
      console.log('***************'+this.conclusion)
      this.dateEnd=new Date();
      let timeDiff=this.dateEnd-this.dateBegin;
      console.log('*******************************'+timeDiff);
      //提示信息
      this.loading = false;
      this.$message({
        type: "success",
        message: "计算结束,耗时"+timeDiff+"ms,请查看结果",
      });

    },
    //上传失败
    fileUploadError() {
      this.loading = false;
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