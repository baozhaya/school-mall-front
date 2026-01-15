<template>
   <div class = "card" style="margin-bottom: 5px">
     <el-input  style="width: 240px" v-model="date.name" placeholder="请不要输入我没有的东西，谢谢" :prefix-icon="Search"/>
     <el-input  style="width: 240px" v-model="date.account" placeholder="余额查询" :prefix-icon="Search"/>
     <el-button @click="load" type="primary" style="margin:0 5px">查询</el-button>
     <el-button @click="reset" type="info"  >重置</el-button>
   </div>
    <div  class = "card" style="margin-bottom: 5px">
      <div style ="margin-bottom:5px">
        <el-button @click="handleAdd" type="primary">新增</el-button>
      </div>
      <el-table :data="date.tableDate" stripe style="width: 100%">
        <el-table-column prop="username" label="账号"  />
        <el-table-column prop="name" label="姓名"  />
        <el-table-column prop="avatar" label="头像" >
          <template  #default="scope">
            <img v-if="scope.row.avatar" style="width: 50px; height :50px ; display:block;border-radius: 50%"
                 :src=" scope.row.avatar" preview-src-list="[scope.row.avatar]" preview-teleported>
          </template>
        </el-table-column>
        <el-table-column prop="role" label="角色" />
        <el-table-column prop="account" label="余额" />
        <el-table-column lable="操作" width="180" fix="right">
          <template #default="scope">
           <el-button type="primary"@click="handleEdit(scope.row)">编辑</el-button>
           <el-button type="danger" @click="del(scope.row.id)">删除</el-button>
           </template>
        </el-table-column>
      </el-table>
    </div>
  <div class="card">
    <el-pagination  v-model:current-page="date.pageNum" v-model:page-size="date.pageSize"
                   @current-change="load"
        background layout="total,  prev, pager, next" :total="date.total"/>
  </div>

    <el-dialog v-model="date.formVisible" title="用户信息" width=40% destroy-on-close>
        <el-form ref="formRef" :model="date.form":rules="date.rules" label-width="80px"style="padding-right:40px">
          <el-form-item prop="username" label="账号" >
            <el-input :disabled="date.form.id !== undefined" v-model="date.form.username"placeholder="请输入账号" autocomplete="off" ></el-input>
          </el-form-item>
            <el-form-item prop="name" label="姓名" >
              <el-input v-model="date.form.name"placeholder="请输入姓名" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item label="余额" >
            <el-input v-model="date.form.account"placeholder="请输入余额" autocomplete="off" ></el-input>
          </el-form-item>
          <el-form-item prop="avatar" label="头像" >
            <el-upload
                v-model:file-list="fileList"
                :action="baseUrl + '/files/upload'"
                list-type="picture"
                :on-success="handleFileUpload">
              <el-button type="primary">点击上传</el-button>
            </el-upload>
          </el-form-item>
        </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="date.formVisible = false">取消</el-button>
          <el-button type="primary" @click="save">确定</el-button>
        </span>
      </template>
    </el-dialog>


</template>

<script setup>//数据操作
import { reactive,ref } from 'vue';
import {Search} from "@element-plus/icons-vue"
import request from "@/utils/request"
import { ElMessage, ElMessageBox } from "element-plus";


const baseUrl= import.meta.env.VITE_BASE_URL
const formRef = ref()
const date = reactive({
  name:null,
  tableDate:[],
  total:0,
  pageNum:1,
  pageSize:5,
  account:"",
  formVisible:false,
  form:{},
  rules:{
    username: [
      { required: true, message: '请输入账号', trigger: 'blur' },
          ]
  }
})
//分页查询数据的函数
const load=() =>{
  request.get('user/selectPage',{
    params:{
      pageNum:date.pageNum,
      pageSize:date.pageSize,
      name:date.name,
      account:date.account,
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
date.name='null'
  date.account='null'
load()
}
const  del = (id) => {
  ElMessageBox.confirm('确定删除吗','删除确认',{type:'warning'}).then(()=>{
    request.delete('/user/delete/'+id).then(res => {
      if (res.code == '200'){
        ElMessage.success('操作成功')
        load()
      }
      else{
        ElMessage.error(res.msg)
      }
    })
  }).catch(err =>{})

  }

  const handleAdd=()=>{
  date.form = {}
    date.formVisible = true
  }
const add =() =>{
  request.post('/user/add',date.form).then(res=> {
    if (res.code == '200') {
      ElMessage.success('操作成功')
      date.formVisible = false
      load()
    } else {
      ElMessage.error(res.msg)
    }
  })
}
  const handleEdit=(row)=>{
  date.form=JSON.parse(JSON.stringify(row))
    date.formVisible=false
  }

  const update =() =>{
    request.put('/user/update',date.form).then(res=> {
      if (res.code == '200') {
        ElMessage.success('操作成功')
        date.formVisible = false
        load()
      } else {
        ElMessage.error(res.msg)
      }
    })
  }

  const save=()=>{
        formRef.value.validate(valid=>{
          if (valid){  //表示表单校验通过
            if(date.form.id){
            update()
            }else{
              add()
            }   //或者写成三目表达是：date.form.id ? upadte():add()
          }
        })

  }

    //表单上传头像的函数 response.avatar就是头像的url
  const handleFileUpload=(response)=>{
    date.form.avatar=response.data;
  }

</script>