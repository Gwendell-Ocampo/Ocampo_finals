<template>
  <v-app>
    <v-navigation-drawer
        v-if="route.path !== '/login'"
        v-model="drawer"
        expand-on-hover
        permanent
        rail
      >
        <v-list>
          <v-list-item
            subtitle="gwendellocampo6@gmail.com"
            title="Gwendell Ocampo"
          ></v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list density="compact" nav>
          <v-list-item
            prepend-icon="mdi-folder"
            title="Dashboard"
            value="myfiles"
            to="/"
          ></v-list-item>
          <v-list-item
            prepend-icon="mdi-table-edit"
            title="Editable Data Grid"
            value="data-grid"
            to="/data-grid"
          ></v-list-item>
        </v-list>
      </v-navigation-drawer>

      <v-app-bar v-if="route.path !== '/login'" color="primary">
        <v-app-bar-nav-icon variant="text" @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-app-bar-title>System Integration</v-app-bar-title>
      </v-app-bar>

    <v-main>
      <slot />
    </v-main>

  </v-app>
</template>

<script lang="ts" setup>
import { ref } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const drawer = ref(true)
</script>

<style>

</style>