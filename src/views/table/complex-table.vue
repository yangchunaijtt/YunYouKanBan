<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input v-model.trim="listQuery.ProjectName" clearable placeholder="项目名称" style="width: 200px" class="filter-item" @keyup.enter.native="handleFilter" @clear="projectName" />
      <el-input v-model.trim="listQuery.ProjectManager" clearable placeholder="项目经理" style="width: 200px" class="filter-item" @keyup.enter.native="handleFilter" @clear="projectManager" />
      <el-select v-model="listQuery.ProjectState" placeholder="项目状态" clearable class="filter-item" style="width: 130px" @clear="projectState">
        <el-option v-for="item in calendarTypeOptions" :key="item.key" :label="item.display_name" :value="item.key" />
      </el-select>
      <el-button v-waves class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-button class="filter-item" style="margin-left: 10px" type="info" icon="el-icon-delete" @click="removeSearch">
        清空搜索
      </el-button>
      <el-button class="filter-item" style="margin-left: 10px" type="primary" icon="el-icon-edit" @click="handleCreate">
        新增
      </el-button>

    </div>
    <el-table v-loading="listLoading" :data="list" :row-class-name="tableRowClassName" default-expand-all row-key="id" :tree-props="treeProps" :header-cell-style="discountHeaderStyle" border fit
      highlight-current-row>
      <!-- <el-table v-loading="listLoading" row-key="title" :header-cell-style="discountHeaderStyle" :data="list" border fit highlight-current-row style="width: 100%" @sort-change="sortChange"> -->
      <!-- <el-table-column label="ID" prop="id" sortable="custom" align="center" width="80" :class-name="getSortClass('id')">
        <template slot-scope="{row}">
          <span>{{ row.id }}</span>
        </template>
      </el-table-column> -->
      <!-- <el-table-column label="时间" width="150px" align="center">
        <template slot-scope="{row}">
          <span>{{ row.timestamp | parseTime('{y}-{m}-{d} {h}:{i}') }}</span>
        </template>
      </el-table-column> -->
      <el-table-column label="项目" align="center">
        <el-table-column label="项目名称" min-width="200px" align="start">
          <!-- <template> -->
          <template slot-scope="{ row }">
            <span>{{ row.ProjectName }}</span>
            <!-- <span>{{ row.title }}</span> -->
          </template>
        </el-table-column>
        <el-table-column label="项目状态" width="110px" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.ProjectState }}</span>
          </template>
        </el-table-column>
        <el-table-column label="项目经理" width="110px" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.ProjectManager }}</span>
          </template>
        </el-table-column>
        <!-- <el-table-column label="项目计划" align="center" width="300">
          <template slot-scope="{ row }">
            <span>{{ row.ProjectPlan }}</span>
          </template>
        </el-table-column> -->
      </el-table-column>
      <el-table-column label="工时评估" align="center">
        <el-table-column label="计划开始时间" class-name="status-col" width="110">
          <template slot-scope="{ row }">
            <span v-if="row.PlanStartTime">{{ row.PlanStartTime | parseTime('{y}/{m}/{d}') }}</span>
          </template>
        </el-table-column>
        <el-table-column label="计划结束时间" class-name="status-col" width="110">
          <template slot-scope="{ row }">
            <span v-if="row.PlanEndTime">{{ row.PlanEndTime | parseTime('{y}/{m}/{d}') }}</span>
          </template>
        </el-table-column>
        <el-table-column label="计划工时" class-name="status-col" width="100">
          <template slot-scope="{ row }">
            <span>{{ row.PlanWorktime }}</span>
          </template>
        </el-table-column>
        <el-table-column label="实际开始时间" class-name="status-col" width="110">
          <template slot-scope="{ row }">
            <span v-if="row.ActualStartTime">{{ row.ActualStartTime | parseTime('{y}/{m}/{d}') }}</span>
          </template>
        </el-table-column>
        <el-table-column label="实际结束时间" class-name="status-col" width="110">
          <template slot-scope="{ row }">
            <span v-if="row.ActualEndTime">{{ row.ActualEndTime | parseTime('{y}/{m}/{d}') }}</span>
          </template>
        </el-table-column>
        <el-table-column label="实际工时" class-name="status-col">
          <template slot-scope="{ row }">
            <span>{{ row.ActualWorkTime }}</span>
          </template>
        </el-table-column>
        <el-table-column label="工时利用率" class-name="status-col" width="110">
          <template slot-scope="{ row }">
            <span>{{ row.UtilizationRate }}</span>
          </template>
        </el-table-column>
      </el-table-column>
      <el-table-column label="工作量评估" align="center">
        <el-table-column label="总工作包" class-name="status-col">
          <template slot-scope="{ row }">
            <span>{{ row.TotalWorkPackage }}</span>
          </template>
        </el-table-column>
        <el-table-column label="已完成" class-name="status-col">
          <template slot-scope="{ row }">
            <span>{{ row.Completeness }}</span>
          </template>
        </el-table-column>
        <el-table-column label="未完成" class-name="status-col">
          <template slot-scope="{ row }">
            <span>{{ row.UnFinish }}</span>
          </template>
        </el-table-column>
        <el-table-column label="完成度" class-name="status-col">
          <template slot-scope="{ row }">
            <span>{{ row.OffTheStocks }}</span>
          </template>
        </el-table-column>
      </el-table-column>
      <el-table-column label="偏差度" align="center">
        <el-table-column label="" class-name="status-col">
          <template slot-scope="{ row }">
            <span>{{ row.Ref1 }}</span>
          </template>
        </el-table-column>
        <el-table-column label="" class-name="status-col">
          <template slot-scope="{ row }">
            <span v-if="row.Ref1">{{ row.Deflection }}</span>
          </template>
        </el-table-column>
      </el-table-column>
      <el-table-column label="紧急度" class-name="status-col">
        <template slot-scope="{ row }">
          <span>{{ row.Urgency }}</span>
        </template>
      </el-table-column>
      <el-table-column label="备注" class-name="status-col" width="300">
        <template slot-scope="{ row }">
          <span>{{ row.Note }}</span>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="250" class-name="small-padding fixed-width">
        <template slot-scope="{ row, $index }">
          <el-button v-if="row.ParentId === 0" type="primary" size="mini" @click="handleCreateChild(row,row.ParentId)">
            新增子计划
          </el-button>
          <el-button type="primary" size="mini" @click="handleUpdate(row)">
            编辑
          </el-button>
          <el-button v-if="row.status != 'deleted'" size="mini" type="danger" @click="handleDelete(row.id, $index)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- <pagination v-show="total > 0" :total="total" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getList" /> -->
    <!-- 编辑 -->
    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" width="55%">
      <el-form ref="dataForm" :rules="rules" :model="temp">
        <!-- <el-form ref="dataForm" :rules="rules" :model="temp" label-position="left" label-width="70px" style="width: 400px; margin-left: 50px"> -->
        <div class="create-form">
          <el-form-item label="项目名称" style="width:30%" prop="ProjectName">
            <el-input v-if="dialogStatus === 'update'" v-model="temp.ProjectName" placeholder="请输入项目名称" :disabled="true" />
            <el-input v-else-if="dialogStatus === 'createChild'" v-model="temp.ProjectName" placeholder="请输入项目名称" :disabled="true" />
            <el-input v-else-if="dialogStatus === 'create'" v-model="temp.ProjectName" placeholder="请输入项目名称" />
          </el-form-item>
          <el-form-item label="项目状态" prop="ProjectState">
            <el-select v-model="temp.ProjectState" class="filter-item" placeholder="请选择状态">
              <el-option v-for="item in statusOptions" :key="item" :label="item" :value="item" />
            </el-select>
          </el-form-item>
          <el-form-item label="项目经理" prop="ProjectManager">
            <el-input v-if="dialogStatus === 'update'" v-model="temp.ProjectManager" placeholder="请输入项目经理" :disabled="true" />
            <el-input v-else-if="dialogStatus === 'createChild'" v-model="temp.ProjectManager" placeholder="请输入项目经理" :disabled="true" />
            <el-input v-else-if="dialogStatus === 'create'" v-model="temp.ProjectManager" placeholder="请输入项目经理" />
          </el-form-item>
        </div>
        <!-- <el-form-item label="项目计划">
          <el-input v-model="temp.ProjectPlan" :autosize="{ minRows: 2, maxRows: 4 }" type="textarea" placeholder="请输入项目计划" />
        </el-form-item> -->
        <div class="create-form">
          <el-form-item label="计划开始时间" prop="PlanStartTime">
            <el-date-picker v-if="dialogStatus === 'update'" v-model="temp.PlanStartTime" type="date" value-format="yyyy-MM-dd" :disabled="true" placeholder="选择计划开始时间" />
            <el-date-picker v-else v-model="temp.PlanStartTime" type="date" value-format="yyyy-MM-dd" placeholder="选择计划开始时间" />
          </el-form-item>
          <el-form-item label="计划结束时间" prop="PlanEndTime">
            <el-date-picker v-if="dialogStatus === 'update'" v-model="temp.PlanEndTime" type="date" value-format="yyyy-MM-dd" placeholder="选择计划结束时间" />
            <el-date-picker v-else v-model="temp.PlanEndTime" type="date" value-format="yyyy-MM-dd" placeholder="选择计划结束时间" />
          </el-form-item>
          <el-form-item label="计划工时" prop="PlanWorktime" style="width:20%">
            <el-input v-model="temp.PlanWorktime" placeholder="请输入计划工时" @keyup.native="numberPlanWorktime" />
          </el-form-item>
        </div>
        <div class="create-form">
          <el-form-item label="实际开始时间" prop="ActualStartTime">
            <el-date-picker v-model="temp.ActualStartTime" type="date" value-format="yyyy-MM-dd" placeholder="选择实际开始时间" />
          </el-form-item>
          <el-form-item label="实际结束时间" prop="ActualEndTime">
            <el-date-picker v-model="temp.ActualEndTime" type="date" value-format="yyyy-MM-dd" placeholder="选择实际结束时间" />
          </el-form-item>
          <el-form-item label="实际工时" prop="ActualWorkTime" style="width:20%">
            <el-input v-model="temp.ActualWorkTime" placeholder="请输入实际工时" @keyup.native="numberActualWorkTime" />
          </el-form-item>
        </div>
        <div class="create-form">
          <el-form-item label="工时利用率" prop="UtilizationRate" style="width:15%">
            <el-input v-model="temp.UtilizationRate" :disabled="true" />
          </el-form-item>
          <el-form-item label="总工作包" prop="TotalWorkPackage" style="width:10%">
            <el-input v-model="temp.TotalWorkPackage" placeholder="请输入" @keyup.native="numberTotalWorkPackage" />
          </el-form-item>
          <el-form-item label="已完成" prop="Completeness" style="width:10%">
            <el-input v-model="temp.Completeness" placeholder="请输入" @keyup.native="numberCompleteness" />
          </el-form-item>
          <el-form-item label="未完成" prop="UnFinish" style="width:10%">
            <el-input v-model="temp.UnFinish" :disabled="true" />
          </el-form-item>
          <el-form-item label="完成度" prop="OffTheStocks" style="width:10%">
            <el-input v-model="temp.OffTheStocks" :disabled="true" />
          </el-form-item>
          <el-form-item label="偏差度" prop="Ref1" style="width:10%">
            <el-input v-model="temp.Ref1" :disabled="true" />
          </el-form-item>
          <el-form-item label="紧急度" prop="Urgency" style="width:10%">
            <el-input v-model="temp.Urgency" placeholder="请输入" />
          </el-form-item>
        </div>

        <el-form-item label="备注">
          <el-input v-model="temp.Note" :autosize="{ minRows: 2, maxRows: 4 }" type="textarea" placeholder="请输入备注" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false"> 取消 </el-button>
        <el-button type="primary" @click="dialogStatus === 'create' ? createData() : dialogStatus === 'update' ? updateData() : createChildData(temp.id)">
          <!-- <el-button type="primary" @click="dialogStatus === 'create' ? createData() : updateData()"> -->
          确认
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { fetchList, createArticle, updateArticle, listDelete } from '@/api/article'
import waves from '@/directive/waves' // waves directive
// eslint-disable-next-line no-unused-vars
import { parseTime } from '@/utils'
// import Pagination from '@/components/Pagination' // secondary package based on el-pagination

const calendarTypeOptions = [
  { key: '未开始', display_name: '未开始' },
  { key: '开发中', display_name: '开发中' },
  { key: '已完成', display_name: '已完成' }
]

export default {
  name: 'ComplexTable',
  // components: { Pagination },
  directives: { waves },
  data() {
    return {
      list: null,
      total: 0,
      listLoading: true,
      listQuery: {
        // page: 1,
        // limit: 5,
        // importance: undefined,
        // title: undefined,
        // type: undefined,
        // sort: '+id'
        ProjectName: '',
        ProjectState: '',
        ProjectManager: ''
      },
      treeProps: { children: 'BordList' },
      calendarTypeOptions,
      statusOptions: ['未开始', '开发中', '已完成'],
      temp: {
        id: 0,
        ParentId: 0,
        ProjectName: '',
        // 项目状态
        ProjectState: '',
        // 项目经理
        ProjectManager: '',
        // 项目计划
        ProjectPlan: '',
        // 计划开始时间
        PlanStartTime: '',
        // 计划结束时间
        PlanEndTime: '',
        // 计划工时
        PlanWorktime: '',
        // 实际开始时间
        ActualStartTime: '',
        // 实际结束时间
        ActualEndTime: '',
        // 实际工时
        ActualWorkTime: '',
        // 工时利用率
        UtilizationRate: '',
        //  总工作包
        TotalWorkPackage: '',
        // 已完成
        Completeness: '',
        //  未完成
        UnFinish: '',
        //  完成度
        OffTheStocks: '',
        //  偏差度
        Ref1: '',
        //  紧急度
        Urgency: '',
        // 备注
        Note: ''
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '新增',
        createChild: '新增子计划'
      },
      dialogPvVisible: false,
      rules: {
        ProjectManager: [{ required: true, message: '请输入项目经理', trigger: 'blur' }],
        ProjectState: [{ required: true, message: '请选择项目类型', trigger: 'change' }],
        ProjectName: [{ required: true, message: '请输入项目名称', trigger: 'blur' }],
        PlanStartTime: [{ required: true, message: '请选择计划开始时间', trigger: 'change' }],
        PlanEndTime: [{ required: true, message: '请选择计划结束时间', trigger: 'change' }],
        PlanWorktime: [{ required: true, message: '请输入计划工时', trigger: 'blur' }]
      },
      downloadLoading: false
    }
  },
  watch: {
    temp: {
      handler(newval, old) {
        // 工时率
        const UtilizationRate = Math.round(parseFloat((newval.ActualWorkTime) / parseFloat(newval.PlanWorktime) * 10000) / 100.00)
        newval.UtilizationRate = (newval.ActualWorkTime && newval.PlanWorktime ? UtilizationRate + '%' : '')
        // 完成度
        const OffTheStocks = Math.round(parseFloat((newval.Completeness) / parseFloat(newval.TotalWorkPackage) * 10000) / 100.00)
        newval.OffTheStocks = (newval.Completeness && newval.TotalWorkPackage ? OffTheStocks + '%' : '')
        // 未完成
        const UnFinish = Math.round(parseFloat(newval.TotalWorkPackage) - parseFloat(newval.Completeness))
        newval.UnFinish = (newval.Completeness && newval.TotalWorkPackage ? UnFinish : '')
        //
        const xxx = parseFloat((newval.Completeness) / parseFloat(newval.TotalWorkPackage) * 10000) / 100.00
        const yyy = parseFloat((newval.ActualWorkTime) / parseFloat(newval.PlanWorktime) * 10000) / 100.00
        const Ref1 = Math.round(parseFloat(xxx) - parseFloat(yyy))
        newval.Ref1 = (newval.OffTheStocks && newval.UtilizationRate ? Ref1 + '%' : '')
      },
      deep: true
    }
  },
  created() {
    this.getList()
  },

  mounted() {
    setInterval(() => { this.getList() }, 28800000)
  },
  methods: {
    // 搜索框的时实触发
    projectName() {
      this.listQuery.ProjectName = ''
      this.getList()
    },
    projectState() {
      this.listQuery.ProjectState = ''
      this.getList()
    },
    projectManager() {
      this.listQuery.ProjectManager = ''
      this.getList()
    },
    tableRowClassName({ row, rowIndex }) {
      // console.log(row.Ref1)
      var str = row.Ref1.replace('%', '') / 100
      // console.log(str)
      if (str > 0.1) {
        row.Deflection = '项目超前'
        return 'project-advance'
      } else if (str < -0.1) {
        row.Deflection = '项目延期'
        return 'project-delay'
      } else {
        row.Deflection = '项目正常'
      }
      // return ''
    },
    // 输入数字正则
    numberTotalWorkPackage() {
      this.temp.TotalWorkPackage = this.temp.TotalWorkPackage.replace(/[^\d.]/g, '')
    },
    numberActualWorkTime() {
      this.temp.ActualWorkTime = this.temp.ActualWorkTime.replace(/[^\d.]/g, '')
    },
    numberPlanWorktime() {
      this.temp.PlanWorktime = this.temp.PlanWorktime.replace(/[^\d.]/g, '')
    },
    numberCompleteness() {
      this.temp.Completeness = this.temp.Completeness.replace(/[^\d.]/g, '')
    },
    removeSearch() {
      this.listQuery.ProjectName = ''
      this.listQuery.ProjectState = ''
      this.listQuery.ProjectManager = ''
      this.getList()
    },
    discountHeaderStyle({ row, column, rowIndex, columnIndex }) {
      if (rowIndex === 4 || rowIndex === 8 || rowIndex === 2) {
        return { display: 'none' }
      }
    },

    getList() {
      this.listLoading = true
      fetchList(this.listQuery).then((response) => {
        const dataSorting = response.DataList.sort(function(a, b) {
          if (a.ProjectState === '已完成') {
            return a.PlanStartTime < b.PlanStartTime ? 1 : -1
          } else {
            return a.PlanStartTime > b.PlanStartTime ? 1 : +1
          }
        })
        this.list = dataSorting
        // this.total = response.data.total
        // console.log(response)
        this.listLoading = false
      })
    },
    handleFilter() {
      this.listQuery.ProjectName || this.listQuery.ProjectState || this.listQuery.ProjectManager ? this.getList() : ''
    },
    resetTemp() {
      this.temp = {
        id: undefined,
        importance: 1,
        remark: '',
        timestamp: new Date(),
        title: '',
        status: '',
        type: ''
      }
    },
    handleCreate() {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    // 新增子计划
    handleCreateChild(row, pad) {
      this.resetTemp()
      this.temp.ProjectName = row.ProjectName
      this.temp.id = row.id
      this.temp.ProjectManager = row.ProjectManager
      // this.temp = Object.assign({}, row) // copy obj
      this.dialogStatus = 'createChild'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    createData() {
      // console.log(this.temp)
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          createArticle(this.temp).then(() => {
            this.dialogFormVisible = false
            this.$notify({
              title: '成功',
              message: '创建成功',
              type: 'success',
              duration: 2000
            })
            this.getList()
          })
        }
      })
    },
    // 确认添加子计划
    createChildData(id) {
      this.temp.ParentId = id
      this.temp.id = 0
      createArticle(this.temp).then(() => {
        // this.list.unshift(this.temp)
        // console.log(this.temp)
        this.dialogFormVisible = false
        this.$notify({
          title: '成功',
          message: '添加子计划成功',
          type: 'success',
          duration: 2000
        })
        this.getList()
      })
      // }
      // })
    },
    handleUpdate(row) {
      this.temp = Object.assign({}, row) // copy obj
      this.temp.timestamp = new Date(this.temp.timestamp)
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    // 确认编辑
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          const tempData = Object.assign({}, this.temp)
          // console.log(this.temp)
          // tempData.timestamp = +new Date(tempData.timestamp) // change Thu Nov 30 2017 16:41:05 GMT+0800 (CST) to 1512031311464
          updateArticle(tempData).then(() => {
            // const index = this.list.findIndex((v) => v.id === this.temp.id)
            // this.list.splice(index, 1, this.temp)
            this.dialogFormVisible = false
            this.$notify({
              title: '成功',
              message: '更新成功',
              type: 'success',
              duration: 2000
            })
            this.getList()
          })
        }
      })
    },
    // 删除
    handleDelete(id, index) {
      this.$confirm('是否确认删除, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const delete_id = {}
        delete_id.id = id
        const tempData = Object.assign({}, delete_id)
        listDelete(tempData).then(() => {
          this.dialogFormVisible = false
          this.getList()
        })

        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    getSortClass(key) {
      const sort = this.listQuery.sort
      return sort === `+${key}` ? 'ascending' : 'descending'
    }
  }
}
</script>
