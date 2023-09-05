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

// const paydayCountdown = computed(()=>{
//   const value = configSetting.value?.payday
//   const date = new Date(currentTime.value)
//   console.log('date is ???',date);
  
// })

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
  const settingString = localStorage.getItem(SETTING_ITEM_NAME)
  if(settingString){
    const settingMap = JSON.parse(settingString)
    configSetting.value = settingMap
    console.log('当前配置',configSetting.value);
    
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
      <div class="body">
        <div class="countdown-item payoff">
          <p>距离<span class="countdown-item-name">发工资
            </span>还有
            <span class="countdown-item-time">25</span>天
            <span class="countdown-item-time">14</span>小时
            <span class="countdown-item-time">20</span>分钟
            <span class="countdown-item-time">30</span>秒
          </p>
        </div>
        <div class="countdown-item" v-for="item in countdownText">
          <p>距离<span class="countdown-item-name">
            {{ item[0] }}</span>假期还有<span class="countdown-item-time">{{ item[1]?item[1]:0 }}</span>天
          </p>
        </div>
        <div class="countdown-item payoff">
          <p v-if="Array.isArray(comeOffWorkTime)">距离<span class="countdown-item-name">下班
            </span>还有
            <span class="countdown-item-time">{{ comeOffWorkTime[0] }}</span>小时
            <span class="countdown-item-time">{{ comeOffWorkTime[1] }}</span>分钟
            <span class="countdown-item-time">{{ comeOffWorkTime[2] }}</span>秒
          </p>
          <p v-else>下班啦！！！！</p>
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
        <div class="buttom-left">当前时间：2023-08-15 23：26</div>
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
      <Setting 
        v-if="showSetting"
        @close-setting="showSetting = false"
        :configSetting="configSetting"
      />

  </div>
</template>

<style scoped  lang="less">
@import './main.less';
</style>
