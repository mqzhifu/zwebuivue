<template>
  <div>
    <div class="gva-form-box">
      <el-form :model="formData" ref="elFormRef" label-position="right" :rules="rule" label-width="80px">
        <el-form-item label="名称:" prop="name">
          <el-input v-model="formData.name" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="平台类型1自有2阿里3腾讯4华为5声网:" prop="platform">
          <el-select v-model="formData.platform" placeholder="请选择" :clearable="true">
            <el-option v-for="(item,key) in PLATFORMOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="环境变量,1本地2开发3测试4预发布5线上:" prop="env">
          <el-select v-model="formData.env" placeholder="请选择" :clearable="true">
            <el-option v-for="(item,key) in ENVOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="主机地址:" prop="host">
          <el-input v-model="formData.host" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="用户名:" prop="user">
          <el-input v-model="formData.user" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="密码:" prop="ps">
          <el-input v-model="formData.ps" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="主机端口号:" prop="port">
          <el-input v-model="formData.port" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="自定义配置信息:" prop="ext">
          <el-input v-model="formData.ext" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="价格:" prop="price">
          <el-input v-model.number="formData.price" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="负责人姓名:" prop="chargeUserName">
          <el-input v-model="formData.chargeUserName" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="开始时间:" prop="startTime">
          <el-input v-model.number="formData.startTime" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="结束时间:" prop="endTime">
          <el-input v-model.number="formData.endTime" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="状态1正常2关闭3异常:" prop="status">
          <el-select v-model="formData.status" placeholder="请选择" :clearable="true">
            <el-option v-for="(item,key) in PROJECT_STATUSOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
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
  name: 'Instance'
}
</script>

<script setup>
import {
  createInstance,
  updateInstance,
  findInstance
} from '@/api/instance'

// 自动获取字典
import { getDictFunc } from '@/utils/format'
import { useRoute, useRouter } from "vue-router"
import { ElMessage } from 'element-plus'
import { ref, reactive } from 'vue'
const route = useRoute()
const router = useRouter()

const type = ref('')
const PLATFORMOptions = ref([])
const ENVOptions = ref([])
const PROJECT_STATUSOptions = ref([])
const formData = ref({
            name: '',
            platform: undefined,
            env: undefined,
            host: '',
            user: '',
            ps: '',
            port: '',
            ext: '',
            price: 0,
            chargeUserName: '',
            startTime: 0,
            endTime: 0,
            status: undefined,
        })
// 验证规则
const rule = reactive({
               name : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               platform : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               env : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               host : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               user : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               status : [{
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
      const res = await findInstance({ ID: route.query.id })
      if (res.code === 0) {
        formData.value = res.data.reinstance
        type.value = 'update'
      }
    } else {
      type.value = 'create'
    }
    PLATFORMOptions.value = await getDictFunc('PLATFORM')
    ENVOptions.value = await getDictFunc('ENV')
    PROJECT_STATUSOptions.value = await getDictFunc('PROJECT_STATUS')
}

init()
// 保存按钮
const save = async() => {
      elFormRef.value?.validate( async (valid) => {
         if (!valid) return
            let res
           switch (type.value) {
             case 'create':
               res = await createInstance(formData.value)
               break
             case 'update':
               res = await updateInstance(formData.value)
               break
             default:
               res = await createInstance(formData.value)
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
