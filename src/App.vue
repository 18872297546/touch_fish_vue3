<script setup lang="ts">
import Main from './components/main.vue'
import 'element-plus/dist/index.css'
</script>

<template>
  <el-config-provider namespace="touch">
    <Main/>
  </el-config-provider>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
