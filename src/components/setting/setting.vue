<script setup lang="ts">
import {onMounted, ref} from 'vue'
import {SETTING_ITEM_NAME} from 'constant/common'
import characterMap from 'constant/character'
const characterUpDefaultTime = new Date(2000, 1, 1, 12, 0, 0)
const props = defineProps<{
    configSetting:any
}>()

const emit = defineEmits(['close-setting'])

const settingConfig = ref({
    comeOffWorkTime:'',
    characterUp:'',
    payday:5,
    characterName: 0
})

const onSubmit = () => {
    const settingString = JSON.stringify(settingConfig.value)
    localStorage.setItem(SETTING_ITEM_NAME,settingString)
    emit('close-setting')
}

onMounted(()=>{
    if (props.configSetting) {
        settingConfig.value =  props.configSetting
    }
})

</script>

<template>
    <el-form :model="settingConfig" >
        <el-form-item label="设置下班时间">
            <el-time-select 
                v-model="settingConfig.comeOffWorkTime" 
                placeholder="请选择时间"     
                start="06:00"
                step="00:15"
                end="23:00"/>
        </el-form-item>
        <el-form-item label="设置要复刻的角色">
            <el-select v-model="settingConfig.characterName" filterable placeholder="Select">
                <el-option
                    v-for="item in characterMap"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                />
            </el-select>
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