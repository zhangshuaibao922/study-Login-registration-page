<template>
    <div style="width: 100vw;height: 100vh;overflow: hidden;display: flex">
        <div style="flex: 1">
            <el-image style="width: 100%;height: 100%;" fit="cover"
                      src="https://img.zcool.cn/community/01193959eeec64a801202b0c23804b.jpg@1280w_1l_2o_100sh.jpg"></el-image>
        </div>
        <div class="welcome-title">
            <div style="font-weight: bold;font-size: 30px">欢迎来到学习平台</div>
            <div style="margin-top: 10px">在这里你可以学习如何使用Java，如何搭建网站，并且与Java之父密切交流</div>
            <div style="margin-top: 5px">在这里你可以同性交流</div>
        </div>
        <div style="width: 400px;background-color:white;z-index: 1">
            <router-view v-slot="{ Component }">
                <transition>
                    <component :is="Component" />
                </transition>
            </router-view>
        </div>
    </div>
</template>

<script setup>
</script>

<style scoped>
.welcome-title {
    position: absolute;
    bottom: 30px;
    left: 30px;
    color: white;
    text-shadow: 0 0 10px black;
}
</style>