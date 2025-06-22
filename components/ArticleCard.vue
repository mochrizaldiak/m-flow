<script setup>
import { computed } from 'vue'
import { marked } from 'marked'

const props = defineProps({
  title: String,
  content: String,
  category: String,
  date: String
})

const truncatedContent = computed(() => {
  if (!props.content) return ''
  const rawHTML = marked.parse(props.content)
  const temp = document.createElement('div')
  temp.innerHTML = rawHTML
  const text = temp.textContent || temp.innerText || ''
  const preview = text.length > 100 ? text.slice(0, 100) + '...' : text
  return preview
})
</script>

<template>
  <div class="article-card">
    <div class="article-meta">
      {{ category }} •
      {{ new Date(date).toLocaleDateString('id-ID', {
        year: 'numeric', month: 'long', day: 'numeric'
      }) }}
    </div>
    <div class="article-title">{{ title }}</div>
    <div class="article-content">{{ truncatedContent }}</div>
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
