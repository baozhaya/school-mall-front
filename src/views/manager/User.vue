<template>
  <div>
   <div class = "card" style="margin-bottom: 5px">
     <el-input  style="width: 240px" v-model="date.name" placeholder="请不要输入我没有的东西，谢谢" :prefix-icon="Search"/>
     <el-button @click="load" type="primary" style="margin:0 5px">查询</el-button>
     <el-button @click="reset" type="info"  >重置</el-button>
   </div>
    <div  class = "card" style="margin-bottom: 5px">
      <div style ="margin-bottom:5px">
        <el-button @click="reset" type="primary">新增</el-button>
      </div>
      <el-table :data="date.tableDate" stripe style="width: 100%">
        <el-table-column prop="username" label="账号"  />
        <el-table-column prop="name" label="姓名"  />
        <el-table-column prop="role" label="角色" />
        <el-table-column prop="account" label="余额" />
        <el-table-column lable="操作" width="180" fix="right">
          <el-button type="primary">编辑</el-button>
          <el-button type="danger">删除</el-button>
        </el-table-column>
      </el-table>
    </div>
  <div class="card">
    <el-pagination  v-model:current-page="date.pageNum" v-model:page-size="date.pageSize"
                   @current-change="load"
        background layout="total,  prev, pager, next" :total="date.total"/>
  </div>
  </div>
</template>
<script setup>//数据操作
import { reactive } from 'vue';
import {Search} from "@element-plus/icons-vue"
import request from "@/utils/request"

const date = reactive({
  name:null,
  tableDate:[],
  total:0,
  pageNum:1,
  pageSize:5,
})
//分页查询数据的函数
const load=() =>{
  request.get('user/selectPage',{
    params:{
      pageNum:date.pageNum,
      pageSize:date.pageSize,
    }
  }).then(res=>{
    if (res.code == '200'){
    date.tableDate=res.data.list
    date.total = res.data.total
    }
    else{
    ElMessage.error(res.msg)
    }
      })
}
load()
const reset=()=>{

}
</script>