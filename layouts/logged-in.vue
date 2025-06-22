<script setup>
import { useRoute } from 'vue-router'
import {
  Home,
  Wallet,
  Newspaper,
  User
} from 'lucide-vue-next'

const route = useRoute()
</script>

<template>
  <div class="layout-container">
    <main class="main-content">
      <NuxtPage />
    </main>

    <nav class="bottom-nav">
      <NuxtLink to="/" :class="{ active: route.path === '/' || route.path.startsWith('/budget') }">
        <Home size="20" />
      </NuxtLink>

      <NuxtLink to="/transaction" :class="{ active: route.path.startsWith('/transaction') }">
        <Wallet size="20" />
      </NuxtLink>

      <NuxtLink to="/article" :class="{ active: route.path.startsWith('/article') }">
        <Newspaper size="20" />
      </NuxtLink>

      <NuxtLink to="/profile" :class="{ active: route.path === '/profile' }">
        <User size="20" />
      </NuxtLink>
    </nav>
  </div>
</template>

<style scoped>
.layout-container {
  max-width: 375px;
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: #fff;
}

.main-content {
  flex: 1;
  padding-bottom: 60px;
}

.bottom-nav {
  position: fixed;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 375px;
  height: 56px;
  border-top: 1px solid #ddd;
  background-color: #fff;
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.bottom-nav a {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: #888;
  text-decoration: none;
}

.bottom-nav a.active {
  color: #2c7be5;
}
</style>
