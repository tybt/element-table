<template>
  <TableColumn
    :key="item.prop"
    :prop="item.prop"
    :label="item.label"
    :width="item.width"
    :fixed="item.fixed"
    :sortable="item.sortable"
  >
    <template v-if="item.children">
      <MyColumns
        v-for="(childItem,childIndex) in item.children"
        :key="childIndex"
        :item="childItem"
        :item-index="childIndex"
        v-bind="$attrs"
        @tableClick="tableClick"
      />
    </template>
    <template v-if="!item.children" slot-scope="scope">
      <div v-if="item.type === 'button'" class="cellButton">
        <span
          v-for="(elem) in getButtons(item.buttons,scope.row)"
          :key="elem"
          class="buttons"
          @click="tableClick({index:itemIndex, name:elem, row: scope.row, label: scope.column.label})"
          v-html="elem"
        />
      </div>
      <slot v-if="item.type==='slot'" :name="item.prop" :row="scope.row" />
      <Tooltip v-else :open-delay="1500" class="item" effect="dark" :content="getValue(item.value,scope)" placement="top-start">
        <div
          class="commonLabel"
          :style="{color:getColor(item.color,scope.row),cursor:item.color?'pointer':'',width:'100%'}"
          @click="tableClick({index:itemIndex, name:getValue(item.value,scope), row: scope.row, label: scope.column.label})"
          v-html="getValue(item.value,scope)"
        />
      </Tooltip>
    </template>
  </TableColumn>

</template>

<script>
import { TableColumn, Tooltip } from 'element-ui'
export default {
  name: 'MyColumns',
  components: {
    TableColumn, Tooltip
  },
  props: {
    item: {
      type: Object,
      default: () => ({})
    },
    itemIndex: {
      type: Number
    }

  },
  mounted () {
  },
  methods: {
    tableClick({ index, name, row, label }) {
      this.$emit('tableClick', { index, name, row, label })
    },
    getValue(value, scope) {
      if (value) {
        if (typeof value === 'function') {
          return value({ row: scope.row })
        }
        return value
      }
      return scope.row[scope.column.property]
    },
    getColor(color, row) {
      if (typeof color === 'function') {
        return color({ row })
      }
      return color
    },
    getButtons(buttons, row) {
      if (typeof buttons === 'function') {
        return buttons({ row })
      }
      return buttons
    }
  }

}
</script>
<style lang="scss" scoped>
  .cellButton{
    display: flex;
    align-items: center;
    flex-wrap: nowrap;
    justify-content:center;
  }

</style>
