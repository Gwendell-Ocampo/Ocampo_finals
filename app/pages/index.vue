<template>
  <div class="dashboard-page d-flex align-center justify-center pa-4">
    <v-card width="560" class="pa-6 text-center" rounded="xl" elevation="8">
      <v-card-title>Dashboard</v-card-title>
      <v-card-text>Welcome to System Integration.</v-card-text>
      <v-btn color="error" prepend-icon="mdi-logout" @click="handleSignOut">
        Sign out
      </v-btn>
    </v-card>
  </div>
</template>

<script lang="ts" setup>
// @ts-nocheck

definePageMeta({
  layout: 'default',
  middleware: ['auth']
})

import { useRouter } from 'vue-router'

const router = useRouter()

const handleSignOut = async () => {
  localStorage.removeItem('google_user')
  await router.push('/login')
}
</script>

<style>
.dashboard-page {
  min-height: calc(100vh - 64px);
}

</style>