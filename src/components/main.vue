<script setup lang="ts">
import { ref } from 'vue'
const tableData = [
  {
    date: '2016-05-03',

  },
  {
    date: '2016-05-02',

  },
  {
    date: '2016-05-04',

  },  {
    date: '2016-05-01',

  },
  {
    date: '2016-05-01',

  },
]


</script>

<template>
  <div class="common-layout">
      <div class="main-header">
        <div class="blue-bar"></div>
        <div class="content">这是一啥的这是一句鸡汤啥的这是一句鸡汤啥的这是一句鸡汤啥的这是一句鸡汤啥的</div>
      </div>
      <div class="table-body">
        <el-table :data="tableData" stripe style="width: 100%" :show-header="false">
          <el-table-column prop="date" label="Date" width="180" />
          <el-table-column prop="name" label="Name" width="180" />
          <el-table-column prop="address" label="Address" />
        </el-table>
      </div>
  </div>
</template>

<style scoped  lang="less">
@import './main.less';
</style>
