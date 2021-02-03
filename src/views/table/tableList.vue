<template>
  <div class="app-container">
    <el-table v-loading="listLoading" :data="list" :row-class-name="tableRowClassName" default-expand-all row-key="id" :tree-props="{children: 'BordList'}" :header-cell-style="discountHeaderStyle" border fit highlight-current-row>
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
    </el-table>
  </div>
</template>

<script>
import { fetchList } from '@/api/article'
import waves from '@/directive/waves' // waves directive
// import Pagination from '@/components/Pagination' // secondary package based on el-pagination

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
      dialogFormVisible: false,
      dialogStatus: '',
      dialogPvVisible: false,
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
    }
  }
}
</script>
