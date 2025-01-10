<script lang="ts">
import { UploadFilled } from '@element-plus/icons-vue';
import { ElMessage } from 'element-plus';
import axios from 'axios';
import 'viewerjs/dist/viewer.css';
import Viewer from 'v-viewer';
import 'element-plus/theme-chalk/el-message-box.css';
import { api as viewerApi } from "v-viewer"
import { ref } from 'vue'

export default{ 
  data(){
    return {
      tableData:[],
      svgUrl:"",
      viewerOptions: {
        inline: true, // 启用内联模式
        toolbar: true, // 显示工具栏
        navbar: false, // 显示导航栏
        button: false, // 显示按钮
        title: false, // 隐藏标题
        transition: false,
        zoomRatio: 0.5
      },images:[],
  }
},  directives: {
    viewer: Viewer.directive,
  },
  methods:
  {
    handleUploadSuccess(response, file, fileList){
    console.log('上传成功', response);
    ElMessage({
    message: '上传成功.',
    type: 'success',
  });
  this.tableData.push( response);

},  handleError(err, file, fileList) {
      console.error('上传失败', err);
    }, handleEdit(index, id){


  const url = '/api/convert';

// 3. 定义请求的参数
const data = {
  id:id
};

// 4. 定义请求的配置（可选）
const config = {
  headers: {
    'Content-Type': 'application/json', // 设置请求头
  },
  timeout: 50000, // 设置超时时间
};

axios
  .post(url, data, config)
  .then((response) => {
    // 请求成功，处理响应数据
    this.svgUrl =  response.data
    this.images=[];
    this.images.push(response.data);
    console.log('请求成功，响应数据：', response.data);
  })
  .catch((error) => {
    // 请求失败，处理错误
    if (error.response) {
      // 请求已发出，但服务器返回了错误状态码（如 4xx、5xx）
      console.error('服务器返回错误：', error.response.data);
      console.error('状态码：', error.response.status);
    } else if (error.request) {
      // 请求已发出，但没有收到响应
      console.error('未收到响应：', error.request);
    } else {
      // 其他错误（如请求配置错误）
      console.error('请求配置错误：', error.message);
    }
  });
},
handleDelete(index){
  this.tableData.splice(index, 1)

}
  }

};
</script>

<template>
<el-row>
    <el-col :span="8" :offset="8">
      <div class="grid-content ep-bg-purple" >
        <el-upload class="upload-demo"
    :show-file-list="false"
    :on-success="handleUploadSuccess"
    :on-error="handleError"
    accept=".dwg"
    drag
    action="/api/upload">
    <el-icon class="el-icon--upload"><upload-filled /></el-icon>
    <div class="el-upload__text">
      拖放 或者 <em> 点击上传dwg格式文件</em>
    </div>
  </el-upload>
      </div>
    </el-col>  
  </el-row>
  <el-row> 
    <el-col :span="8" :offset="8">
      <el-table :data="tableData" style="width: 100%">
    <el-table-column  width="240">
      <template #default="scope">
        <el-popover effect="light" trigger="hover" placement="top" width="auto">
          <template #default>
            <div>name: {{ scope.row.name }}</div>
          </template>
          <template #reference>
            <el-tag>{{ scope.row.name }}</el-tag>
          </template>
        </el-popover>
      </template>
    </el-table-column>
    <el-table-column width="auto" header-align="right" align="right">
      <template #default="scope">
        <el-button
        type="success"
        size="small" @click="handleEdit(scope.$index, scope.row.id)">
          转换
        </el-button>
        <el-button
          size="small"
          type="danger"
          @click="handleDelete(scope.$index)">
          移除
        </el-button>
      </template>
    </el-table-column>
  </el-table>
  </el-col>
</el-row>
<el-row>
    <el-col :span="24">
      <div>    
        <viewer :images="images" :options="viewerOptions" class="hidden-thumbnails">
          <img v-for="src in images" :key="src" :src="src" >
       </viewer>  
      </div>
    </el-col>
  </el-row>
</template>
<style scoped>
.hidden-thumbnails
{
  height: 600px;
  width: 100%;
}
.hidden-thumbnails img {
  display: none; /* 隐藏缩略图 */
  width: 0 px;

}

</style>
