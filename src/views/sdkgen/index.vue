<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">企业名称</label>
        <el-input v-model="query.companyName" clearable placeholder="企业名称" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="企业名称" prop="companyName">
            <el-input v-model="form.companyName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="证书信息" prop="certInfo">
            <el-input v-model="form.certInfo" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="sdk路径" prop="sdkPath">
            <el-input v-model="form.sdkPath" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="companyName" label="企业名称" />
        <el-table-column prop="certInfo" label="证书信息" />
        <el-table-column prop="sdkPath" label="sdk路径" />
        <el-table-column v-if="checkPer(['admin','sdkInfo:edit','sdkInfo:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <el-button type="text" style="margin-left: -1px" size="mini" @click="toGen(scope.row.tableName)">生成</el-button>
            <el-button size="mini" style="margin-left: -1px;margin-right: 2px" type="text" @click="toDownload(scope.row.tableName)">下载</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudSdkInfo from '@/api/sdkInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, companyName: null, certInfo: null, sdkPath: null }
export default {
  name: 'SdkInfo',
  components: { pagination, crudOperation, rrOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: 'SDK生成', url: 'api/sdkInfo', idField: 'id', sort: 'id,desc', crudMethod: { ...crudSdkInfo }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'sdkInfo:add'],
        edit: ['admin', 'sdkInfo:edit'],
        del: ['admin', 'sdkInfo:del']
      },
      rules: {
        companyName: [
          { required: true, message: '企业名称不能为空', trigger: 'blur' }
        ],
        certInfo: [
          { required: true, message: '证书信息不能为空', trigger: 'blur' }
        ],
        sdkPath: [
          { required: true, message: 'sdk路径不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'companyName', display_name: '企业名称' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
