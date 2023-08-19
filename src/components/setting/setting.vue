<script setup lang="ts">
import {ref} from 'vue'
const characterUpDefaultTime = new Date(2000, 1, 1, 12, 0, 0)
const emit = defineEmits(['close-setting'])
const settingConfig = ref({
    closingTime:'',
    characterUp:'',
    payday:''
})

const onSubmit = () => {
    console.log('settingConfig is ??',settingConfig.value);
    emit('close-setting')
}

</script>

<template>
    <el-form :model="settingConfig">
        <el-form-item label="设置下班时间">
            <el-time-picker v-model="settingConfig.closingTime" placeholder="Arbitrary time" />
        </el-form-item>
        <el-form-item label="设置角色复刻时间">
            <el-date-picker 
                v-model="settingConfig.characterUp" 
                type="datetime" 
                placeholder="Arbitrary time"
                :default-time="characterUpDefaultTime" />
        </el-form-item>
        <el-form-item label="设置发薪日">
            <el-time-picker v-model="settingConfig.payday" placeholder="Arbitrary time" />
        </el-form-item>
        <el-form-item>
            <el-button type="primary" @click="onSubmit">保存设置</el-button>
        </el-form-item>
    </el-form>

</template>

<style lang="less" src="./setting.less"></style>