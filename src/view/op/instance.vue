<template>
  <div>
    <div class="gva-search-box">
      <el-form :inline="true" :model="searchInfo" class="demo-form-inline">
           <el-form-item label="平台类型1自有2阿里3腾讯4华为5声网" prop="platform">
            <el-select v-model="searchInfo.platform" clearable placeholder="请选择" @clear="()=>{searchInfo.platform=undefined}">
              <el-option v-for="(item,key) in PLATFORMOptions" :key="key" :label="item.label" :value="item.value" />
            </el-select>
            </el-form-item>
           <el-form-item label="环境变量,1本地2开发3测试4预发布5线上" prop="env">
            <el-select v-model="searchInfo.env" clearable placeholder="请选择" @clear="()=>{searchInfo.env=undefined}">
              <el-option v-for="(item,key) in ENVOptions" :key="key" :label="item.label" :value="item.value" />
            </el-select>
            </el-form-item>
        <el-form-item label="主机地址">
          <el-input v-model="searchInfo.host" placeholder="搜索条件" />
        </el-form-item>
           <el-form-item label="状态1正常2关闭3异常" prop="status">
            <el-select v-model="searchInfo.status" clearable placeholder="请选择" @clear="()=>{searchInfo.status=undefined}">
              <el-option v-for="(item,key) in PROJECT_STATUSOptions" :key="key" :label="item.label" :value="item.value" />
            </el-select>
            </el-form-item>
        <el-form-item>
          <el-button size="small" type="primary" icon="search" @click="onSubmit">查询</el-button>
          <el-button size="small" icon="refresh" @click="onReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="gva-table-box">
        <div class="gva-btn-list">
            <el-button size="small" type="primary" icon="plus" @click="openDialog">新增</el-button>
            <el-popover v-model:visible="deleteVisible" placement="top" width="160">
            <p>确定要删除吗？</p>
            <div style="text-align: right; margin-top: 8px;">
                <el-button size="small" type="primary" link @click="deleteVisible = false">取消</el-button>
                <el-button size="small" type="primary" @click="onDelete">确定</el-button>
            </div>
            <template #reference>
                <el-button icon="delete" size="small" style="margin-left: 10px;" :disabled="!multipleSelection.length" @click="deleteVisible = true">删除</el-button>
            </template>
            </el-popover>
        </div>
        <el-table
        ref="multipleTable"
        style="width: 100%"
        tooltip-effect="dark"
        :data="tableData"
        row-key="ID"
        @selection-change="handleSelectionChange"
        >
        <el-table-column type="selection" width="55" />
        <el-table-column align="left" label="日期" width="180">
            <template #default="scope">{{ formatDate(scope.row.CreatedAt) }}</template>
        </el-table-column>
        <el-table-column align="left" label="名称" prop="name" width="120" />
        <el-table-column align="left" label="平台类型1自有2阿里3腾讯4华为5声网" prop="platform" width="120">
            <template #default="scope">
            {{ filterDict(scope.row.platform,PLATFORMOptions) }}
            </template>
        </el-table-column>
        <el-table-column align="left" label="环境变量,1本地2开发3测试4预发布5线上" prop="env" width="120">
            <template #default="scope">
            {{ filterDict(scope.row.env,ENVOptions) }}
            </template>
        </el-table-column>
        <el-table-column align="left" label="主机地址" prop="host" width="120" />
        <el-table-column align="left" label="用户名" prop="user" width="120" />
        <el-table-column align="left" label="密码" prop="ps" width="120" />
        <el-table-column align="left" label="主机端口号" prop="port" width="120" />
        <el-table-column align="left" label="自定义配置信息" prop="ext" width="120" />
        <el-table-column align="left" label="价格" prop="price" width="120" />
        <el-table-column align="left" label="负责人姓名" prop="chargeUserName" width="120" />
        <el-table-column align="left" label="开始时间" prop="startTime" width="120" />
        <el-table-column align="left" label="结束时间" prop="endTime" width="120" />
        <el-table-column align="left" label="状态1正常2关闭3异常" prop="status" width="120">
            <template #default="scope">
            {{ filterDict(scope.row.status,PROJECT_STATUSOptions) }}
            </template>
        </el-table-column>
        <el-table-column align="left" label="按钮组">
            <template #default="scope">
            <el-button type="primary" link icon="edit" size="small" class="table-button" @click="updateInstanceFunc(scope.row)">变更</el-button>
            <el-button type="primary" link icon="delete" size="small" @click="deleteRow(scope.row)">删除</el-button>
            </template>
        </el-table-column>
        </el-table>
        <div class="gva-pagination">
            <el-pagination
            layout="total, sizes, prev, pager, next, jumper"
            :current-page="page"
            :page-size="pageSize"
            :page-sizes="[10, 30, 50, 100]"
            :total="total"
            @current-change="handleCurrentChange"
            @size-change="handleSizeChange"
            />
        </div>
    </div>
    <el-dialog v-model="dialogFormVisible" :before-close="closeDialog" title="弹窗操作">
      <el-form :model="formData" label-position="right" ref="elFormRef" :rules="rule" label-width="80px">
        <el-form-item label="名称:"  prop="name" >
          <el-input v-model="formData.name" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="平台类型1自有2阿里3腾讯4华为5声网:"  prop="platform" >
          <el-select v-model="formData.platform" placeholder="请选择" style="width:100%" :clearable="true" >
            <el-option v-for="(item,key) in PLATFORMOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="环境变量,1本地2开发3测试4预发布5线上:"  prop="env" >
          <el-select v-model="formData.env" placeholder="请选择" style="width:100%" :clearable="true" >
            <el-option v-for="(item,key) in ENVOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="主机地址:"  prop="host" >
          <el-input v-model="formData.host" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="用户名:"  prop="user" >
          <el-input v-model="formData.user" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="密码:"  prop="ps" >
          <el-input v-model="formData.ps" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="主机端口号:"  prop="port" >
          <el-input v-model="formData.port" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="自定义配置信息:"  prop="ext" >
          <el-input v-model="formData.ext" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="价格:"  prop="price" >
          <el-input v-model.number="formData.price" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="负责人姓名:"  prop="chargeUserName" >
          <el-input v-model="formData.chargeUserName" :clearable="true"  placeholder="请输入" />
        </el-form-item>
        <el-form-item label="开始时间:"  prop="startTime" >
          <el-input v-model.number="formData.startTime" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="结束时间:"  prop="endTime" >
          <el-input v-model.number="formData.endTime" :clearable="true" placeholder="请输入" />
        </el-form-item>
        <el-form-item label="状态1正常2关闭3异常:"  prop="status" >
          <el-select v-model="formData.status" placeholder="请选择" style="width:100%" :clearable="true" >
            <el-option v-for="(item,key) in PROJECT_STATUSOptions" :key="key" :label="item.label" :value="item.value" />
          </el-select>
        </el-form-item>
      </el-form>
      <template #footer>
        <div class="dialog-footer">
          <el-button size="small" @click="closeDialog">取 消</el-button>
          <el-button size="small" type="primary" @click="enterDialog">确 定</el-button>
        </div>
      </template>
    </el-dialog>
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
  deleteInstance,
  deleteInstanceByIds,
  updateInstance,
  findInstance,
  getInstanceList
} from '@/api/instance'

// 全量引入格式化工具 请按需保留
import { getDictFunc, formatDate, formatBoolean, filterDict } from '@/utils/format'
import { ElMessage, ElMessageBox } from 'element-plus'
import { ref, reactive } from 'vue'

// 自动化生成的字典（可能为空）以及字段
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


// =========== 表格控制部分 ===========
const page = ref(1)
const total = ref(0)
const pageSize = ref(10)
const tableData = ref([])
const searchInfo = ref({})

// 重置
const onReset = () => {
  searchInfo.value = {}
}

// 搜索
const onSubmit = () => {
  page.value = 1
  pageSize.value = 10
  getTableData()
}

// 分页
const handleSizeChange = (val) => {
  pageSize.value = val
  getTableData()
}

// 修改页面容量
const handleCurrentChange = (val) => {
  page.value = val
  getTableData()
}

// 查询
const getTableData = async() => {
  const table = await getInstanceList({ page: page.value, pageSize: pageSize.value, ...searchInfo.value })
  if (table.code === 0) {
    tableData.value = table.data.list
    total.value = table.data.total
    page.value = table.data.page
    pageSize.value = table.data.pageSize
  }
}

getTableData()

// ============== 表格控制部分结束 ===============

// 获取需要的字典 可能为空 按需保留
const setOptions = async () =>{
    PLATFORMOptions.value = await getDictFunc('PLATFORM')
    ENVOptions.value = await getDictFunc('ENV')
    PROJECT_STATUSOptions.value = await getDictFunc('PROJECT_STATUS')
}

// 获取需要的字典 可能为空 按需保留
setOptions()


// 多选数据
const multipleSelection = ref([])
// 多选
const handleSelectionChange = (val) => {
    multipleSelection.value = val
}

// 删除行
const deleteRow = (row) => {
    ElMessageBox.confirm('确定要删除吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
    }).then(() => {
            deleteInstanceFunc(row)
        })
    }


// 批量删除控制标记
const deleteVisible = ref(false)

// 多选删除
const onDelete = async() => {
      const ids = []
      if (multipleSelection.value.length === 0) {
        ElMessage({
          type: 'warning',
          message: '请选择要删除的数据'
        })
        return
      }
      multipleSelection.value &&
        multipleSelection.value.map(item => {
          ids.push(item.ID)
        })
      const res = await deleteInstanceByIds({ ids })
      if (res.code === 0) {
        ElMessage({
          type: 'success',
          message: '删除成功'
        })
        if (tableData.value.length === ids.length && page.value > 1) {
          page.value--
        }
        deleteVisible.value = false
        getTableData()
      }
    }

// 行为控制标记（弹窗内部需要增还是改）
const type = ref('')

// 更新行
const updateInstanceFunc = async(row) => {
    const res = await findInstance({ ID: row.ID })
    type.value = 'update'
    if (res.code === 0) {
        formData.value = res.data.reinstance
        dialogFormVisible.value = true
    }
}


// 删除行
const deleteInstanceFunc = async (row) => {
    const res = await deleteInstance({ ID: row.ID })
    if (res.code === 0) {
        ElMessage({
                type: 'success',
                message: '删除成功'
            })
            if (tableData.value.length === 1 && page.value > 1) {
            page.value--
        }
        getTableData()
    }
}

// 弹窗控制标记
const dialogFormVisible = ref(false)

// 打开弹窗
const openDialog = () => {
    type.value = 'create'
    dialogFormVisible.value = true
}

// 关闭弹窗
const closeDialog = () => {
    dialogFormVisible.value = false
    formData.value = {
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
        }
}
// 弹窗确定
const enterDialog = async () => {
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
                closeDialog()
                getTableData()
              }
      })
}
</script>

<style>
</style>
