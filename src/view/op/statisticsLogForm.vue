<template>
  <div>
    <div class="gva-form-box">
      <el-form :model="formData" ref="elFormRef" label-position="right" :rules="rule" label-width="80px">
        <el-form-item label="项目ID:" prop="projectId">
          <el-input v-model.number="formData.projectId" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="用户ID:" prop="uid">
          <el-input v-model.number="formData.uid" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="设备-序列号:" prop="sn">
          <el-input v-model="formData.sn" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="动作标识:" prop="action">
          <el-input v-model="formData.action" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="应用名:" prop="appName">
          <el-input v-model="formData.appName" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="应用版本号:" prop="appVersion">
          <el-input v-model="formData.appVersion" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="自定义消息体:" prop="msg">
          <el-input v-model="formData.msg" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="包名:" prop="packageName">
          <el-input v-model="formData.packageName" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="记录时间:" prop="recordTime">
          <el-input v-model.number="formData.recordTime" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="设备-版本号:" prop="systemVersion">
          <el-input v-model="formData.systemVersion" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="http公共请求头信息:" prop="headerCommon">
          <el-input v-model="formData.headerCommon" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="http请求头客户端基础信息:" prop="headerBase">
          <el-input v-model="formData.headerBase" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item>
          <el-button size="mini" type="primary" @click="save">保存</el-button>
          <el-button size="mini" type="primary" @click="back">返回</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'StatisticsLog'
}
</script>

<script setup>
import {
  createStatisticsLog,
  updateStatisticsLog,
  findStatisticsLog
} from '@/api/statisticsLog'

// 自动获取字典
import { getDictFunc } from '@/utils/format'
import { useRoute, useRouter } from "vue-router"
import { ElMessage } from 'element-plus'
import { ref, reactive } from 'vue'
const route = useRoute()
const router = useRouter()

const type = ref('')
const formData = ref({
            projectId: 0,
            uid: 0,
            sn: '',
            action: '',
            appName: '',
            appVersion: '',
            msg: '',
            packageName: '',
            recordTime: 0,
            systemVersion: '',
            headerCommon: '',
            headerBase: '',
        })
// 验证规则
const rule = reactive({
               projectId : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               uid : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               sn : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               action : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
})

const elFormRef = ref()

// 初始化方法
const init = async () => {
 // 建议通过url传参获取目标数据ID 调用 find方法进行查询数据操作 从而决定本页面是create还是update 以下为id作为url参数示例
    if (route.query.id) {
      const res = await findStatisticsLog({ ID: route.query.id })
      if (res.code === 0) {
        formData.value = res.data.restatisticsLog
        type.value = 'update'
      }
    } else {
      type.value = 'create'
    }
}

init()
// 保存按钮
const save = async() => {
      elFormRef.value?.validate( async (valid) => {
         if (!valid) return
            let res
           switch (type.value) {
             case 'create':
               res = await createStatisticsLog(formData.value)
               break
             case 'update':
               res = await updateStatisticsLog(formData.value)
               break
             default:
               res = await createStatisticsLog(formData.value)
               break
           }
           if (res.code === 0) {
             ElMessage({
               type: 'success',
               message: '创建/更改成功'
             })
           }
       })
}

// 返回按钮
const back = () => {
    router.go(-1)
}

</script>

<style>
</style>
