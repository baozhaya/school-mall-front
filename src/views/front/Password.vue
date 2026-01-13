<template>
  <div class="front-container" style="width: width:40%">
    <div class="card" style="padding:20px" >
      <div style="front-size:20px;margin-bottom:20px;text-align:center">
        修改密码
      </div>
      <el-form ref="formRef" :model="date.user":rules="date.rules" label-width="80px"style="padding-right:40px">
        <el-form-item prop="password" label="原密码" >
          <el-input  v-model="date.user.password" autocomplete="off" show-password ></el-input>
        </el-form-item>
        <el-form-item prop="newPassword" label="新密码" >
          <el-input v-model="date.user.newPassword"placeholder="请输入新密码" autocomplete="off" ></el-input>
        </el-form-item>
        <el-form-item prop="confirmPassword" label="确认密码" >
          <el-input v-model="date.user.confirmPassword"placeholder="请确认密码" autocomplete="off" ></el-input>
        </el-form-item>
        <div style="text-align:center">
          <el-button type="primary" size="large" @click="updatePassword">保存</el-button>
        </div>
      </el-form>
    </div>
  </div>

</template>

<script setup>
import {reactive, ref} from "vue";
import request from "@/utils/request";
import router from "@/router";
import {ElMessage} from "element-plus";

const formRef = ref()
const date = reactive({
  user: JSON.parse(localStorage.getItem('system-user') || '{}'),
  rules:{
    password: [
      { required: true, message: '请输入原密码', trigger: 'blur' },
    ],
    newPassword: [
      { required: true, message: '请输入新密码', trigger: 'blur' },
    ],
    confirmPassword: [
      { required: true, message: '请确认密码', trigger: 'blur' },
    ]
  }
})

const updatePassword =() =>{
  if (date.user.newPassword !== date.user.confirmPassword){
    ElMessage.warning('两次输入的新密码不同，请确认');
    return;
  }
  request.put('/updatePassword',date.user).then(res=> {
    if (res.code == '200') {
      ElMessage.success('操作成功')
      logout()
    } else {
      ElMessage.error(res.msg)
    }
  })
}

const logout = () => {
  router.push('/login')
  localStorage.removeItem('system-user')
}

</script>