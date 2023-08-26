<script setup lang="ts">
import {onMounted, ref} from 'vue'
import {SETTING_ITEM_NAME} from 'constant/common'

const characterUpDefaultTime = new Date(2000, 1, 1, 12, 0, 0)
const props = defineProps<{
    configSetting:any
}>()

const emit = defineEmits(['close-setting'])

const settingConfig = ref({
    comeOffWorkTime:'',
    characterUp:'',
    payday:5
})

const onSubmit = () => {
    const settingString = JSON.stringify(settingConfig.value)
    localStorage.setItem(SETTING_ITEM_NAME,settingString)
    emit('close-setting')
}

onMounted(()=>{
    settingConfig.value = props.configSetting
})

</script>

<template>
    <el-form :model="settingConfig">
        <el-form-item label="设置下班时间">
            <el-time-select 
                v-model="settingConfig.comeOffWorkTime" 
                placeholder="请选择时间"     
                start="14:00"
                step="00:15"
                end="22:00"/>
        </el-form-item>
        <el-form-item label="设置角色复刻时间">
            <el-date-picker 
                v-model="settingConfig.characterUp" 
                type="datetime" 
                placeholder="请选择时间和日期"
                :default-time="characterUpDefaultTime"
                value-format="YYYY-MM-DDTHH:mm:ss" />
        </el-form-item>
        <el-form-item label="设置发薪日">
            <el-input-number v-model="settingConfig.payday" :min="1" :max="31"/>
        </el-form-item>
        <el-form-item>
            <el-button type="primary" @click="onSubmit">保存设置</el-button>
        </el-form-item>
        
    </el-form>

</template>

<style lang="less" src="./setting.less"></style>