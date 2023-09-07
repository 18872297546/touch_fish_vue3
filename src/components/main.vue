<script setup lang="ts">
import { ref,computed, onMounted } from 'vue'
import FESTIVAL from 'constant/festival.ts'
import Setting from '@/components/setting/setting.vue'
import { ArrowUpBold ,ArrowDownBold } from '@element-plus/icons-vue'
import {SETTING_ITEM_NAME,GREAT_QUOTE} from 'constant/common'
import {random} from 'lodash'

const currentTime = ref()
const showSetting = ref(false)
const configSetting = ref()
const quoteIndex = ref(0)
const countdownText = computed(()=>{
  return Object.values(FESTIVAL).map(value=>{
    // console.log('key,value is ',key,value);
    const dateList = value.date
    const vaildFestivalList:any = dateList.map((date)=>{
        const festivalDate = Date.parse(date)
        if(festivalDate > currentTime.value) return festivalDate
    }).filter(Boolean)

    const millisecondsForDate = vaildFestivalList[0] - currentTime.value
    const [day] = formatTime(millisecondsForDate)
    
    return [value.name,day]
  })
})

const comeOffWorkTime = computed(()=>{
  const value = configSetting.value?.comeOffWorkTime
  const date = new Date(currentTime.value).toJSON()?.split('T')[0]+'T'+value
  const ms = Date.parse(date)
  const time = ms - currentTime.value
  const text = time > 0 ? formatTime(time): '下班啦'
  if(Array.isArray(text)) text.splice(0,1)
  return text
})

const characterUp = computed(() => {
  const value = configSetting.value?.characterUp
  const time = Date.parse(value) - currentTime.value
  const text = time>0?formatTime(time):'过了'
  return text
})

const countdownToPayday = computed(() => {
  const now = new Date(); // 获取当前日期
  const currentMonth = now.getMonth(); // 获取当前月份（0 到 11）
  const currentYear = now.getFullYear(); // 获取当前年份
  const dayOfMonth = configSetting.value?.payday
  const sixPM = 18
  // 使用当前年份和月份，以及传入的日期构建发工资日期
  const payday = new Date(currentYear, currentMonth, dayOfMonth, sixPM);

  // 如果发工资日期已经过去，将月份加 1，表示下个月的发工资日期
  if (payday < now) {
    payday.setMonth(currentMonth + 1);
  }
  // 计算距离发工资的时间差（毫秒）
  const timeUntilPayday = payday - now;

  return formatTime(timeUntilPayday)[0]
})

const formatTime = (ms:any) => {
  const seconds = Math.floor((ms / 1000) % 60);
  const minutes = Math.floor((ms / 1000 / 60) % 60);
  const hours = Math.floor((ms / (1000 * 60 * 60)) % 24);
  const days = Math.floor(ms / (1000 * 60 * 60 * 24));
  return [days,hours,minutes,seconds]
}

const displayCurrentTime = computed( () =>  {
  const current = new Date(currentTime.value)
  const year = current.getFullYear()
  const month = current.getMonth()+1
  const day = current.getDate()
  const hours = current.getHours()
  const minutes = current.getMinutes()
  const seconds = current.getSeconds()

  const [formatMonth,formatDay,formattedHours,formattedMinutes,formattedSeconds] = padZeroes([month,day,hours,minutes,seconds])


  return `${year}-${formatMonth}-${formatDay}  ${formattedHours}:${formattedMinutes}:${formattedSeconds}`
})

const padZeroes = (numbers: any[]) => {
  // 辅助函数，确保数字始终显示两位数字
  return numbers.map((number: number) => number < 10 ? `0${number}` : number)
}
const initState = () => {
  const settingString = localStorage.getItem(SETTING_ITEM_NAME)
  if(settingString){
    const settingMap = JSON.parse(settingString)
    configSetting.value = settingMap
  }
  currentTime.value = Date.now()
  setInterval(()=>{
    currentTime.value = Date.now()
  },1000)
}

const switchSetting = () => {
  showSetting.value = !showSetting.value
}

onMounted(()=>{
  initState()
  quoteIndex.value = random(0,GREAT_QUOTE.length-1)
})
</script>

<template>
  <div class="common-layout">
      <div class="main-header">
        <div class="blue-bar"></div>
        <div class="content">{{ GREAT_QUOTE[quoteIndex] }}</div>
      </div>
      <div class="body-and-bottom">
        <div class="body">
          <div class="countdown-item" v-for="item in countdownText">
            <p>距离<span class="countdown-item-name">
              {{ item[0] }}</span>假期<span class="countdown-item-time">{{ item[1]?item[1]:0 }}</span>天
            </p>
          </div>
          <div class="countdown-item payoff">
            <p>距离<span class="countdown-item-name">发工资
              </span>还有
              <span class="countdown-item-time">{{ countdownToPayday }}</span>天
            </p>
          </div>
          <div class="countdown-item payoff">
            <p v-if="Array.isArray(comeOffWorkTime)">距离<span class="countdown-item-name">下班
              </span>还有
              <span class="countdown-item-time">{{ comeOffWorkTime[0] }}</span>小时
              <span class="countdown-item-time">{{ comeOffWorkTime[1] }}</span>分钟
              <span class="countdown-item-time">{{ comeOffWorkTime[2] }}</span>秒
            </p>
            <p v-else>下班啦！自由啦！</p>
          </div>
  
          <div class="countdown-item payoff">
            <p v-if="Array.isArray(characterUp)">距离<span class="countdown-item-name">{{ configSetting?.characterName }}
              </span>复刻还有
              <span class="countdown-item-time">{{ characterUp[0] }}</span>天
              <span class="countdown-item-time">{{ characterUp[1] }}</span>小时
              <span class="countdown-item-time">{{ characterUp[2] }}</span>分钟
              <span class="countdown-item-time">{{ characterUp[3] }}</span>秒
            </p>
            <p v-else>复刻时间已过，请重新设置复刻的角色</p>
          </div>
        </div>
  
        <div class="bottom">
          <div class="buttom-left">当前时间：{{ displayCurrentTime }}</div>
          <div class="buttom-right">
            <el-button 
              type="primary"
              @click="switchSetting"
              :icon="showSetting?ArrowUpBold:ArrowDownBold"
             >
             {{ showSetting?'关闭设置':'打开设置' }}
            </el-button>
          </div>
        </div>
      </div>
      <Setting 
        v-if="showSetting"
        :configSetting="configSetting"
      />

  </div>
</template>

<style scoped  lang="less">
@import './main.less';
</style>
