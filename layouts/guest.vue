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
  min-height: 100vh;
  background: linear-gradient(to bottom right, #6c92f4, #b4c5f8);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 16px;
}
</style>
