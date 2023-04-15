<template>
    <div>
        <div style="margin: 30px 20px">
            <el-steps :active="active" finish-status="success" align-center>
                <el-step title="验证邮箱"/>
                <el-step title="重置密码"/>
            </el-steps>
        </div>
        <transition name="el-fade-in-linear" mode="out-in">
            <div style="text-align: center;margin: 0 20px;height: 100%" v-if="active === 0">
                <div style="text-align: center;margin-top: 100px">
                    <div style="font-size: 25px;font-weight: bold">重置密码</div>
                    <div style="font-size: 14px;color: grey">请输入需要重置密码的电子邮箱地址</div>
                </div>
                <div style="margin-top: 50px">
                    <el-form :model="form" :rules="rules" @validate="onValidate" ref="formRef">
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
                                    <el-input v-model="form.code" :maxlength="6" type="email"
                                              placeholder="请输入验证码">
                                        <template #prefix>
                                            <el-icon>
                                                <EditPen/>
                                            </el-icon>
                                        </template>
                                    </el-input>
                                </el-col>
                                <el-col :span="6">
                                    <el-button type="success" @click="validateEmail"
                                               :disabled="!isEmailValid || coldTime > 0">
                                        {{ coldTime > 0 ? '请稍后 ' + coldTime + '秒' : ' 获取验证码' }}
                                    </el-button>
                                </el-col>
                            </el-row>
                        </el-form-item>
                    </el-form>
                </div>
                <div style="margin-top: 70px">
                    <el-button @click="startReset()" style="width: 270px;" type="danger" plain>开始重置密码</el-button>
                </div>
            </div>
        </transition>
        <transition name="el-fade-in-linear" mode="out-in">
            <div style="text-align: center;margin: 0 20px;height: 100%" v-if="active === 1">
                <div style="text-align: center;margin-top: 100px">
                    <div style="font-size: 25px;font-weight: bold">重置密码</div>
                    <div style="font-size: 14px;color: grey">请填写新密码</div>
                </div>
                <div style="margin-top: 50px">
                    <el-form :model="form" :rules="rules" @validate="onValidate" ref="formRef">
                        <el-form-item prop="password">
                            <el-input v-model="form.password" :maxlength="16" type="password" placeholder="新密码">
                                <template #prefix>
                                    <el-icon>
                                        <Lock/>
                                    </el-icon>
                                </template>
                            </el-input>
                        </el-form-item>
                        <el-form-item prop="password_repeat">
                            <el-input v-model="form.password_repeat" :maxlength="16" type="password"
                                      placeholder="重复新密码">
                                <template #prefix>
                                    <el-icon>
                                        <Lock/>
                                    </el-icon>
                                </template>
                            </el-input>
                        </el-form-item>
                    </el-form>
                </div>
                <div style="margin-top: 70px">
                    <el-button @click="doReset()" style="width: 270px;" type="danger" plain>立即重置密码</el-button>
                </div>
            </div>
        </transition>
    </div>
</template>

<script setup>
import {reactive, ref} from "vue";
import {EditPen, Lock, Message} from "@element-plus/icons-vue";
import {post} from "@/net";
import {ElMessage} from "element-plus";
import router from "@/router";

const active = ref(0)
const form = reactive({
    email: '',
    code: '',
    password: '',
    password_repeat: ''
})
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
    code: [
        {required: true, message: 'Please input code address', trigger: 'blur'},
    ]
}

const formRef = ref()
const isEmailValid = ref(false)
const coldTime = ref(0)
const onValidate = (prop, isValid) => {
    if (prop === 'email')
        isEmailValid.value = isValid
}
const validateEmail = () => {
    coldTime.value = 60
    post("/api/auth/valid-reset-email", {
        email: form.email
    }, (Message) => {
        ElMessage.success(Message)
        setInterval(() => coldTime.value--, 1000)
    }, (Message) => {
        ElMessage.warning(Message)
        coldTime.value = 0
    })
}
const startReset = () => {
    formRef.value.validate((isValid) => {
        if (isValid) {
            post('/api/auth/start-reset', {
                email: form.email,
                code: form.code
            }, () => {
                active.value++
            })
        } else {
            ElMessage.warning("请完整填写内容")
        }
    })
}

const doReset = () => {
    formRef.value.validate((isValid) => {
        if (isValid) {
            post('/api/auth/do-reset', {
                password: form.password
            }, (Message) => {
                ElMessage.success(Message)
                router.push('/')
            })
        } else {
            ElMessage.warning("请填写新的密码")
        }
    })
}
</script>

<style scoped>

</style>