<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { marked } from 'marked' 

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const route = useRoute()
const router = useRouter()
const article = ref(null)
const renderedContent = ref('') 

onMounted(async () => {
  const id = route.params.id
  try {
    const res = await $fetch(`http://localhost:8080/articles/${id}`)
    if (!res) throw new Error('Artikel tidak ditemukan.')

    article.value = res
    renderedContent.value = marked.parse(res.konten || '') 
  } catch (err) {
    console.error(err)
    router.push('/article')
  }
})
</script>

<template>
  <div v-if="article" class="article-wrapper">
    <h1 class="article-title">{{ article.judul }}</h1>
    <div class="article-meta">
      <span class="category">{{ article.kategori }}</span>
      <span>•</span>
      <span class="date">
        {{ new Date(article.tanggal).toLocaleDateString('id-ID', {
          year: 'numeric', month: 'long', day: 'numeric'
        }) }}
      </span>
    </div>
    <div class="article-content" v-html="renderedContent" />
  </div>
</template>

<style scoped>
.article-wrapper {
  padding: 24px 16px;
  max-width: 680px;
  margin: 0 auto;
  background-color: var(--color-white);
}

.article-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 8px;
  color: var(--color-text);
  line-height: 1.3;
}

.article-meta {
  font-size: 13px;
  color: var(--color-text-light);
  margin-bottom: 16px;
  display: flex;
  gap: 6px;
  align-items: center;
}

/* Tambahkan styling markdown */
.article-content {
  font-size: 14px;
  color: var(--color-text);
  line-height: 1.7;
}

.article-content h2, h3, h4 {
  margin-top: 20px;
  font-weight: bold;
}

.article-content p {
  margin: 12px 0;
  text-align: justify;
}

.article-content ul, ol {
  padding-left: 18px;
  margin: 12px 0;
}

.article-content li {
  margin-bottom: 6px;
}

.article-content strong {
  font-weight: bold;
}
</style>
