<script setup>
import ArticleCard from '~/components/ArticleCard.vue'

definePageMeta({ layout: 'logged-in' })

const dummyArticles = [
  {
    id: 1,
    title: 'Cara Mengatur Uang dengan Mudah',
    content: 'Belajar dasar pengelolaan keuangan pribadi...',
    category: 'finance',
    date: '2025-06-16'
  },
  {
    id: 2,
    title: 'Tips Menabung Buat Mahasiswa',
    content: 'Tips sederhana agar bisa konsisten menabung meski uang pas-pasan...',
    category: 'saving',
    date: '2025-06-10'
  }
]
</script>

<template>
  <div class="page-wrapper">
    <h2 class="page-title">Artikel</h2>

    <NuxtLink
      v-for="article in dummyArticles"
      :key="article.id"
      :to="`/article/${article.id}`"
      class="article-link"
    >
      <ArticleCard
        :title="article.title"
        :content="article.content"
        :category="article.category"
        :date="article.date"
      />
    </NuxtLink>
  </div>
</template>

<style scoped>
.page-wrapper {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.page-title {
  font-size: 20px;
  font-weight: bold;
  color: var(--color-text);
}

.article-link {
  text-decoration: none;
  color: inherit;
}
</style>
