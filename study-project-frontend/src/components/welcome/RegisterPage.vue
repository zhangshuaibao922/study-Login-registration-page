<template>
    <div style="text-align: center;margin: 0 20px">
        <div style="text-align: center;margin-top: 150px">
            <div style="font-size: 25px;font-weight: bold">注册新用户</div>
            <div style="font-size: 14px;color: grey">欢迎注册学习平台，请在下方填写相关信息</div>
        </div>
        <div style="margin-top: 50px">
            <el-form :model="form" :rules="rules" @validate="onValidate" ref="formRef">
                <el-form-item prop="username">
                    <el-input v-model="form.username" :maxlength="8" type="text" placeholder="用户名">
                        <template #prefix>
                            <el-icon>
                                <User/>
                            </el-icon>
                        </template>
                    </el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input v-model="form.password" :maxlength="16" type="password" placeholder="密码">
                        <template #prefix>
                            <el-icon>
                                <Lock/>
                            </el-icon>
                        </template>
                    </el-input>
                </el-form-item>
                <el-form-item prop="password_repeat">
                    <el-input v-model="form.password_repeat" :maxlength="16" type="password" placeholder="重复密码">
                        <template #prefix>
                            <el-icon>
                                <Lock/>
                            </el-icon>
                        </template>
                    </el-input>
                </el-form-item>
                <el-form-item prop="email">
                    <el-input v-model="form.email" type="email" placeholder="请输入电子邮件验证码">
                        <template #prefix>
                            <el-icon>
                                <Message/>
                            </el-icon>
                        </template>
                    </el-input>
                </el-form-item>
                <el-form-item prop="code">
                    <el-row :gutter="10">
                        <el-col :span="18">
                            <el-input v-model="form.code" :maxlength="6" type="email"  placeholder="请输入验证码">
                                <template #prefix>
                                    <el-icon>
                                        <EditPen/>
                                    </el-icon>
                                </template>
                            </el-input>
                        </el-col>
                        <el-col :span="6">
                            <el-button type="success" @click="validateEmail" :disabled="!isEmailValid">获取验证码</el-button>
                        </el-col>
                    </el-row>
                </el-form-item>
            </el-form>


        </div>
        <div style="margin-top: 80px">
            <el-button style="width: 270px" type="warning" @click="register">立即注册</el-button>
        </div>
        <div style="margin-top: 20px">
            <span style="font-size: 14px;line-height: 15px;color: grey;">已有账号？ </span>
            <el-link type="primary" style="translate: 0 -2px" @click="router.push('/')">立即登录</el-link>
        </div>
    </div>
</template>

<script setup>
import {Lock, User, Message, EditPen} from "@element-plus/icons-vue";
import router from "@/router";
import {reactive, ref} from "vue";
import {ElMessage} from "element-plus";
import {post} from "@/net";

const form = reactive({
    username: '',
    password: '',
    password_repeat: '',
    email: '',
    code: ''
})
const validateUsername = (rule, value, callback) => {
    if (value === '') {
        callback(new Error('Please input the username'))
    } else if (!/^[a-zA-Z0-9\u4e00-\u9fa5]+$/.test(value)) {
        callback(new Error('only Chinese or Einlish'))
    } else {
        callback()
    }
}
const validatePassword = (rule, value, callback) => {
    if (value === '') {
        callback(new Error('Please input the password again'))
    } else if (value !== form.password) {
        callback(new Error("Two inputs don't match!"))
    } else {
        callback()
    }
}
const rules = {
    username: [
        {validator: validateUsername, trigger: ['blur', 'change']},
        {min: 2, max: 8, message: 'Length should be 2 to 8', trigger: ['blur', 'change']},
    ],
    password: [
        {required: true, message: 'Please input password address', trigger: 'blur'},
        {min: 6, max: 16, message: 'Length should be 6 to 16', trigger: ['blur', 'change']}
    ],
    password_repeat: [
        {required: true, message: 'Please input password address again', trigger: 'blur'},
        {validator: validatePassword, trigger: ['blur', 'change']}
    ],
    email: [
        {required: true, message: 'Please input email address', trigger: 'blur'},
        {type: 'email', message: 'Please input correct email address', trigger: ['blur', 'change']}
    ],
    code:[
        {required: true, message: 'Please input code address', trigger: 'blur'},
    ]
}
const formRef = ref()
const isEmailValid=ref(false)
const onValidate=(prop,isValid)=>{
    if(prop==='email')
        isEmailValid.value=isValid
}
const register = () => {
    formRef.value.validate((isValid) =>{
        if(isValid){
            post('/api/auth/register',{
                username:form.username,
                password:form.password,
                email:form.email,
                code:form.code
            },(Message)=>{
                ElMessage.success(Message)
                router.push("/")
            })
        }else{
            ElMessage.warning("请完整填写注册内容")
        }
    })
}
const validateEmail = () => {
  post("/api/auth/valid-email",{
      email:form.email
  },(Message)=>{
      ElMessage.success(Message)
  })
}

</script>

<style scoped>

</style>