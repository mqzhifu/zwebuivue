<template>
  <div>
    <div class="gva-form-box">
      <el-form :model="formData" ref="elFormRef" label-position="right" :rules="rule" label-width="80px">
        <el-form-item label="名称:" prop="name">
          <el-input v-model="formData.name" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="类型,1service 2frontend 3backend 4app:" prop="type">
          <el-select v-model="formData.type" placeholder="请选择" :clearable="true">
            <el-option v-for="(item,key) in PROJECT_TYPEOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="baseAuth 认证KEY:" prop="access">
          <el-input v-model="formData.access" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="描述信息:" prop="desc">
          <el-input v-model="formData.desc" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="git仓地址:" prop="git">
          <el-input v-model="formData.git" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="实现语言1php2go3java4js:" prop="lang">
          <el-select v-model="formData.lang" placeholder="请选择" :clearable="true">
            <el-option v-for="(item,key) in PROJECT_LANGOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="密钥:" prop="secretKey">
          <el-input v-model="formData.secretKey" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="状态1正常2关闭:" prop="status">
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
  name: 'Project'
}
</script>

<script setup>
import {
  createProject,
  updateProject,
  findProject
} from '@/api/project'

// 自动获取字典
import { getDictFunc } from '@/utils/format'
import { useRoute, useRouter } from "vue-router"
import { ElMessage } from 'element-plus'
import { ref, reactive } from 'vue'
const route = useRoute()
const router = useRouter()

const type = ref('')
const PROJECT_LANGOptions = ref([])
const PROJECT_STATUSOptions = ref([])
const PROJECT_TYPEOptions = ref([])
const formData = ref({
            name: '',
            type: undefined,
            access: '',
            desc: '',
            git: '',
            lang: undefined,
            secretKey: '',
            status: undefined,
        })
// 验证规则
const rule = reactive({
               name : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               type : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               access : [{
                   required: true,
                   message: '',
                   trigger: ['input','blur'],
               }],
               lang : [{
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
      const res = await findProject({ ID: route.query.id })
      if (res.code === 0) {
        formData.value = res.data.reproject
        type.value = 'update'
      }
    } else {
      type.value = 'create'
    }
    PROJECT_LANGOptions.value = await getDictFunc('PROJECT_LANG')
    PROJECT_STATUSOptions.value = await getDictFunc('PROJECT_STATUS')
    PROJECT_TYPEOptions.value = await getDictFunc('PROJECT_TYPE')
}

init()
// 保存按钮
const save = async() => {
      elFormRef.value?.validate( async (valid) => {
         if (!valid) return
            let res
           switch (type.value) {
             case 'create':
               res = await createProject(formData.value)
               break
             case 'update':
               res = await updateProject(formData.value)
               break
             default:
               res = await createProject(formData.value)
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
