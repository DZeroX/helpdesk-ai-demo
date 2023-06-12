<script setup>
  import { ref, computed } from 'vue'
  import Header from './components/Header.vue'
  import Index from './pages/Index.vue';
  import User from './pages/User.vue'
  import Admin from './pages/Admin.vue'
  import NotFound from './pages/404.vue'

  const routes = {
    '/': Index,
    '/User': User,
    '/Admin': Admin
  }

  const currentPath = ref(window.location.hash)

  window.addEventListener('hashchange', () => {
    currentPath.value = window.location.hash
  })

  const currentView = computed(() => {
    return routes[currentPath.value.slice(1) || '/'] || NotFound
  })
</script>

<template>
  <Header />
  <div class="container px-5">
    <component :is="currentView" />
  </div>
</template>

<style scoped>
</style>