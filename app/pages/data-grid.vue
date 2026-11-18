<template>
  <main class="data-grid-page pa-4 pa-md-8">
    <v-card class="data-grid-card mx-auto pa-4 pa-md-6" rounded="xl" elevation="4">
      <div class="d-flex flex-wrap align-center justify-space-between ga-3 mb-2">
        <div>
          <h1 class="text-h5 font-weight-bold">Editable Data Grid</h1>
          <p class="text-body-2 text-medium-emphasis mt-1">
            Click a cell to edit it. You can also add rows using the blank row at the bottom.
          </p>
        </div>
        <v-chip color="primary" variant="tonal" prepend-icon="mdi-table-edit">
          Spreadsheet demo
        </v-chip>
      </div>

      <ClientOnly>
        <EditableDataGrid />
        <template #fallback>
          <div class="grid-loading d-flex align-center justify-center">
            Loading editable grid...
          </div>
        </template>
      </ClientOnly>
    </v-card>
  </main>
</template>

<script setup lang="ts">
definePageMeta({
  middleware: 'auth',
})

useHead({
  title: 'Editable Data Grid',
})
</script>

<style scoped>
.data-grid-page {
  min-height: calc(100vh - 64px);
}

.data-grid-card {
  max-width: 1200px;
}

.grid-loading {
  min-height: 420px;
  border: 1px solid rgba(var(--v-border-color), var(--v-border-opacity));
  border-radius: 8px;
}
</style>
