<script setup>
import {get} from "@/net";
import {ElMessage} from "element-plus";
import {useStore} from "@/stores/counter";
import router from "@/router";

const store =useStore()
if(store.auth.user === null){
    get('/api/user/me', (message) => {
        store.auth.user=message
        router.push('/index')
        ElMessage.success('已登录')
    }, () => {
        store.auth.user=null
        ElMessage.warning('未登录')
    })
}
</script>
<template>
    <router-view></router-view>
</template>

<style scoped>

</style>
