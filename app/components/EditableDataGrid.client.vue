<template>
  <div class="ht-theme-main grid-container">
    <HotTable :settings="hotSettings" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { HotTable } from '@handsontable/vue3'
import { registerAllModules } from 'handsontable/registry'
import type { GridSettings } from 'handsontable/settings'
import 'handsontable/styles/handsontable.css'
import 'handsontable/styles/ht-theme-main.css'

registerAllModules()

const hotSettings = ref<GridSettings>({
  data: [
    ['Wireless Mouse', 'Accessories', 24, 19.99, 'In stock'],
    ['USB-C Hub', 'Accessories', 12, 34.5, 'In stock'],
    ['Mechanical Keyboard', 'Peripherals', 8, 89.99, 'Low stock'],
    ['27-inch Monitor', 'Displays', 5, 229.0, 'Low stock'],
    ['Laptop Stand', 'Accessories', 0, 42.0, 'Out of stock'],
    ['Webcam', 'Peripherals', 16, 59.95, 'In stock'],
  ],
  colHeaders: ['Product', 'Category', 'Quantity', 'Unit price ($)', 'Stock status'],
  rowHeaders: true,
  height: 420,
  width: '100%',
  stretchH: 'all',
  minSpareRows: 1,
  manualColumnResize: true,
  licenseKey: 'non-commercial-and-evaluation',
})
</script>

<style scoped>
.grid-container {
  width: 100%;
}
</style>
