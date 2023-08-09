<script setup lang="ts">
import { ref,computed, onMounted } from 'vue'
import FESTIVAL from 'constant/festival.ts'
const currentTime = ref()

const countdownText = computed(()=>{
  return Object.entries(FESTIVAL).map(([key,value])=>{
    // console.log('key,value is ',key,value);
    const dateList = value.date
    const vaildFestivalList:any = dateList.map((date)=>{
        const festivalDate = Date.parse(date)
        if(festivalDate > currentTime.value) return festivalDate
    }).filter(Boolean)

    const millisecondsForDate = vaildFestivalList[0] - currentTime.value
    const [day, hour, minute, second] = formatTime(millisecondsForDate)
    return { date: `距离${value.name}还有 ${day}天 ${hour}小时${minute}分钟${second}秒` }    
  })
})

const formatTime = (ms:any) => {
  const totalSecond = ms / 1000
  const displaySecond = Math.floor(totalSecond) % 60

  const totalMinute = Math.floor(totalSecond) / 60
  const displayMinute = Math.floor(totalMinute) % 60

  const totalHour = Math.floor(totalMinute) / 60
  const displayHour = Math.floor(totalHour) % 24

  const totalDay = Math.floor(totalHour) / 24
  const displaylDay = Math.floor(totalDay)
  
  return [displaylDay,displayHour,displayMinute,displaySecond]
}

const initState = () => {
  setInterval(()=>{
    currentTime.value = Date.now()
  },1000)
}

onMounted(()=>{
  initState()
})

console.log('FESTIVAL~~~~~~~',FESTIVAL)

</script>

<template>
  <div class="common-layout">
      <div class="main-header">
        <div class="blue-bar"></div>
        <div class="content">这是一啥的这是一句鸡汤啥的这是一句鸡汤啥的这是一句鸡汤啥的这是一句鸡汤啥的</div>
      </div>
      <div class="table-body">
        <el-table :data="countdownText" stripe style="width: 100%" :show-header="false">
          <el-table-column prop="date" label="Date" />
        </el-table>
      </div>
  </div>
</template>

<style scoped  lang="less">
@import './main.less';
</style>
