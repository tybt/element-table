<template>
  <div class="commonTable">
    <div class="flex-row-reverse">
      <img v-if="showExport" class="icons m-l-10" src="../../assets/导出.png" alt="导出表格" srcset="" title="导出表格" @click="exportExcel">
      <SelecPop v-if="!hideConfig" :columns="columns" @changeColumns="changeColumns" />
    </div>
    <Table id="comTable" ref="comTable" v-loading="tableData.loading" stripe :data="tableData.data" border :height="height" highlight-current-row @sort-change="sortChange">
      <TableColumn type="index" width="50" label="序号" />
      <Column
        v-for="(item,itemIndex) in selectedColumns"
        :key="itemIndex"
        :item="item"
        :item-index="itemIndex"
        @tableClick="tableClick"
      >
      <!-- 花了两天时间的成果 -->
        <template v-for="slotName in soltsNames" :slot="slotName" slot-scope="scope">
          <slot :name="slotName" :scope="scope" />
        </template>

      </Column>
    </Table>
    <div class="flex-row-reverse m-t-20">
      <Pagination
        :current-page="tableData.page?tableData.page:1"
        :page-size="10"
        layout="total,  prev, pager, next, jumper"
        :total="tableData.total"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>

  </div>
</template>

<script>
import Column from './Column.vue'
// import { export_json_to_excel, export_table_to_excel } from '@/vendor/Export2Excel'
import SelecPop from './SelecPop.vue'
import { Table, TableColumn, Pagination } from 'element-ui'
export default {
  components: {
    TableColumn,
    Table,
    Pagination,
    SelecPop,
    Column
  },
  props: {
    hideConfig: {
      type: Boolean,
      default: false
    },
    height: {
      type: [String, Number],
      default: 550
    },
    columns: {
      type: Array,
      default: () => []
    },
    tableData: {
      type: Object,
      default: () => ({})
    },
    showExport: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      page: 1,
      selectedColumns: []
    }
  },
  computed: {
    soltsNames() {
      const temp = []
      for (const key in this.$scopedSlots) {
        console.log(key, this.$scopedSlots, 'this.$slots')
        temp.push(key)
      }
      return temp
    }
  },
  created() {
    this.selectedColumns = this.columns.filter(item => !item.isHide)
  },
  methods: {
    exportExcel() {
      this.$emit('exportExcel')
    },
    async generateExcel() {
      await this.$nextTick()
      import('file-saver').then(FileSaver => {
        import('xlsx').then(XLSX => {
          var wb = XLSX.utils.table_to_book(document.querySelector('#comTable'), { raw: true })
          var wbout = XLSX.write(wb, { bookType: 'xlsx', bookSST: true, type: 'array' })
          try {
            FileSaver.saveAs(new Blob([wbout], { type: 'application/octet-stream' }), `表格.xlsx`)
          } catch (e) {
            if (typeof console !== 'undefined') {
              console.log(e, wbout)
            }
          }
          return wbout
        })
      })
    },
    sortChange({ column, prop, order }) {
      console.log({ column, prop, order })
    },

    changeColumns(e) {
      this.selectedColumns = this.columns.filter(item => {
        for (let i = 0; i < e.length; i++) {
          if (item.label === e[i]) {
            return true
          }
        }
        return false
      })
    },
    tableClick({ index, name, row, label }) {
      this.$emit('tableClick', { index, name, row, label })
    },
    handleCurrentChange(e) {
      this.$emit('pageChange', e)
    },
    handleSizeChange(e) {
      console.log('sizeChange', e)
    },
    getButtonType(index) {
      const finallIndex = index % 5
      switch (finallIndex) {
        case 0:
          return 'danger'
        case 1:
          return 'warning'
        case 2:
          return ''
        case 3:
          return 'success'
        case 4:
          return 'info'
        default:
          break
      }
    }
  }
}
</script>

<style lang="scss" >
.icons{
  width: 15px;
  height: 15px;
}
.el-divider--horizontal{
  margin: 0px 0;

}
.commonTable {
  .buttons{
    color: $light-blue;
    margin-right: 10px;
    white-space: nowrap;
  }
  .buttons:hover{
    cursor: pointer;
    opacity: 0.8;
  }
  .el-table{
    font-size: 12px;
  }
  .commonLabel{
    color: #63656e;
    display: inline-block;
    text-overflow: ellipsis;
    white-space:nowrap;
    overflow: hidden;

  }
  .cellButton{
    display: flex;
    align-items: center;
    flex-wrap: nowrap;
    justify-content:center;
  }
  .el-table td,
  .el-table th {
    text-align: center;
  }
.el-table td, .el-table th {
  padding: 8px 0;
}
.el-table .cell{
  display: flex;
  align-items: center;
  justify-content: center;
  line-height: 31px;
}
}
</style>

