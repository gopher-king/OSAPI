<script setup lang="ts">
import { onMounted, ref, reactive } from 'vue'
import { useRouter, onBeforeRouteUpdate } from 'vue-router'
import http from "../../utils/request";
import {useStore} from "../../store";
import { ToolMsg } from "../../utils/ToolMsg";
import {getterLsState, setterLsState} from '../../utils/localStorage'

const store = useStore()
const router = useRouter()

const data = reactive({
  username: getterLsState().username || '游客',
})
const activeIndex = ref('1')
const handleSelect = (key:any, keyPath:any) => {
  // console.log(key, keyPath)
}

// 退出登陆
const logout = () => {
  http.get('/admin/login/logout').then(res => {
    if (res.data.status === 200) {
      data.username = '游客'
      ToolMsg('退出登陆', 'warning')
      router.push('/login')
      localStorage.setItem('token', '')
      localStorage.setItem('osIP', '')
      store.commit('SET_LoginState', false)
      store.commit('SET_NAME', '')
      setterLsState(store)
    } else if (res.data.status === 401) {
      ToolMsg('未登陆', 'warning')
    } else {
      ToolMsg('出错了', 'warning')
    }
  })
}

const isLogin = () => {
  const loginState = store.getters.getLoginState
  if (loginState) {
    data.username = getterLsState().username
  } else {
    data.username = '游客'
    ToolMsg('未登陆-Header', 'warning')
    localStorage.setItem('osIP', '未登陆')
    setterLsState(store)
  }
  return loginState

}
onMounted(() => {
  isLogin()
})

</script>

<template>
  <el-menu
    :default-active="activeIndex"
    class='el-menu-demo pos-relative'
    mode="horizontal"
    background-color="#262F3E"
    text-color="#fff"
    active-text-color="#262F3E"
    @select="handleSelect"
  >
    <el-menu-item index="1" style='width:200px'>
      <el-image
        @click="router.push('/home')"
        style='height:60px'
        src="https://cdn.jsdelivr.net/gh/yesmore/img/img/osapi.png"
        fit="fill"
      ></el-image>
    </el-menu-item>

    <!--<el-menu-item index="2" style='width:100%'>

    </el-menu-item>-->

    <el-sub-menu index="3" class='pos-r-20 pos-absolute'>
      <template #title>{{ data.username }}</template>
      <el-menu-item v-show="data.username==='游客'" @click="router.push('/login')" index="3-1">立即登陆</el-menu-item>
      <el-menu-item disabled v-show="data.username==='游客'" @click="router.push('/register')" index="3-2">新用户注册</el-menu-item>
      <el-menu-item v-show="data.username!=='游客'" @click="logout" index="3-3">注销</el-menu-item>
    </el-sub-menu>
  </el-menu>
</template>

<style scoped lang="less">
.el-menu-demo {
  box-shadow: 1px -1px 12px rgba(128, 128, 128, 0.33);
}
</style>
