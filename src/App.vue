<template>
  <div id="app">
    <!-- 解决vue相同路由跳转强制刷新该路由组件 二次搜索-->
    <router-view :key="$route.fullPath"/>
  </div>
</template>

<script>

  export default {
    name: 'App'
  }
</script>

<style>

</style>
