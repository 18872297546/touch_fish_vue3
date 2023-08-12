<script setup lang="ts">
import { ref,computed, onMounted } from 'vue'
import FESTIVAL from 'constant/festival.ts'
const currentTime = ref()

const countdownText = computed(()=>{
  return Object.values(FESTIVAL).map(value=>{
    // console.log('key,value is ',key,value);
    const dateList = value.date
    const vaildFestivalList:any = dateList.map((date)=>{
        const festivalDate = Date.parse(date)
        if(festivalDate > currentTime.value) return festivalDate
    }).filter(Boolean)

    const millisecondsForDate = vaildFestivalList[0] - currentTime.value
    const [day, hour, minute, second] = formatTime(millisecondsForDate)
    
    return [value.name,day]
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
      <div class="body">
        <div class="countdown-item" v-for="item in countdownText">
          <p>距离<span class="countdown-item-name">{{ item[0] }}</span>还有<span class="countdown-item-time">{{ item[1]?item[1]:0 }}</span>天</p>
        </div>
      </div>
  </div>
</template>

<style scoped  lang="less">
@import './main.less';
</style>
