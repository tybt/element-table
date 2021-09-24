<template>
  <div class="flex-row-reverse">
    <Popover
      v-model="visible"
      placement="right"
      width="560"
    >
      <div class="font-16-b">表格设置</div>
      <Divider />
      <div class=" flex-row-between">
        <span>字段显示设置</span>
        <Checkbox v-model="checkAll" label="全选" @change="handleCheckAllChange" />
      </div>
      <CheckboxGroup v-model="test">
        <Checkbox v-for="item in selectedColumns" :key="item.label" :label="item.label" class="brand" />
      </CheckboxGroup>
      <Divider />
      <div class="flex-row-reverse">
        <Button type="primary" size="mini" @click="changeColums">确定</Button>
        <div class="m-r-20">
          <Button size="mini" @click="visible=false">取消</Button>
        </div>
      </div>
      <i slot="reference" class="el-icon-s-tools" />
    </Popover>
  </div>
</template>
<script>
import { Button, Popover, Checkbox, Divider, CheckboxGroup } from 'element-ui'
export default {
  components: {
    Button,
    Popover,
    Checkbox,
    Divider,
    CheckboxGroup
  },
  props: {
    columns: {
      type: Array,
      default: () => ([])
    }
  },
  data() {
    return {
      selectedColumns: this.columns.filter(item => !item.buttons),
      visible: false,
      test: this.columns.filter(item => (!item.buttons && !item.isHide)).map(item => item.label),
      checkAll: false
    }
  },
  methods: {
    handleCheckAllChange(val) {
      this.test = val ? this.columns.map(item => item.label) : []
    },
    changeColums() {
      const otherColumn = this.columns.filter(item => item.buttons).map(item => item.label)
      this.$emit('changeColumns', [...this.test, ...otherColumn])
      this.visible = false
    }
  }

}
</script>

<style lang="scss" scoped>
.brand{
  margin-right: 20px;
  line-height: 30px;
  font-size: 16px;
  display: inline-block;
}
</style>
