<template>
  <div class="layout-container">
    <main class="main-content">
      <NuxtPage />
    </main>
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
}
</style>
