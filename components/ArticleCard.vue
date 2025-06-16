<script setup>
defineProps({
  title: String,
  content: String,
  category: String,
  date: String
})
</script>

<template>
  <div class="article-card">
    <div class="article-meta">{{ category }} • {{ date }}</div>
    <div class="article-title">{{ title }}</div>
    <div class="article-content">{{ content }}</div>
  </div>
</template>

<style scoped>
.article-card {
  padding: 16px;
  background: var(--color-white);
  border-radius: 10px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
}

.article-meta {
  font-size: 12px;
  color: var(--color-text-light);
  margin-bottom: 6px;
}

.article-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--color-text);
}

.article-content {
  font-size: 14px;
  color: var(--color-text-light);
  margin-top: 4px;
}
</style>
