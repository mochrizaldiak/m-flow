<script setup>
import { ref, computed, onMounted, onBeforeUnmount, watch } from 'vue'
import { useDebounceFn } from '@vueuse/core'
import ArticleCard from '~/components/ArticleCard.vue'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const searchQuery = ref('')
const selectedCategory = ref('all')
const selectedSort = ref('tanggal_desc')
const showBackTop = ref(false)
const headerRef = ref(null)
const articles = ref([])
const loading = ref(false)
const empty = ref(false)

const categories = ['all', 'finance', 'saving', 'budgeting']
const sortOptions = [
  { label: 'A → Z', value: 'judul_asc' },
  { label: 'Z → A', value: 'judul_desc' },
  { label: 'Terbaru', value: 'tanggal_desc' },
  { label: 'Terlama', value: 'tanggal_asc' }
]

const fetchArticles = async () => {
  try {
    loading.value = true
    empty.value = false

    const params = {
      page: 1,
      limit: 100
    }

    if (searchQuery.value) params.search = searchQuery.value
    if (selectedCategory.value !== 'all') params.kategori = selectedCategory.value
    if (selectedSort.value) params.sort = selectedSort.value

    const res = await $fetch('http://localhost:8080/articles/', {
      method: 'GET',
      params
    })

    const data = res?.data || res || []
    articles.value = data
    empty.value = data.length === 0
  } catch (err) {
    console.error('Gagal fetch artikel:', err)
    articles.value = []
    empty.value = true
  } finally {
    loading.value = false
  }
}

const debouncedSearch = useDebounceFn(() => {
  fetchArticles()
}, 500)

watch(searchQuery, () => {
  debouncedSearch()
})

watch([selectedCategory, selectedSort], () => {
  fetchArticles()
})

const resetFilters = () => {
  searchQuery.value = ''
  selectedCategory.value = 'all'
  selectedSort.value = 'newest'
  fetchArticles()
}

const scrollToTop = () => {
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

let observer
onMounted(() => {
  fetchArticles()

  observer = new IntersectionObserver(([entry]) => {
    showBackTop.value = !entry.isIntersecting
  }, { threshold: 0.1 })

  if (headerRef.value) observer.observe(headerRef.value)
})

onBeforeUnmount(() => {
  if (observer && headerRef.value) observer.unobserve(headerRef.value)
})
</script>

<template>
  <div class="page-wrapper">
    <div ref="headerRef" class="header-section">
      <h2 class="page-title">Artikel</h2>

      <input
        v-model="searchQuery"
        type="text"
        class="search-box"
        placeholder="Cari artikel..."
      />

      <div class="filter-bar">
        <select v-model="selectedCategory" class="dropdown">
          <option value="all">Semua Kategori</option>
          <option
            v-for="cat in categories.filter(c => c !== 'all')"
            :key="cat"
            :value="cat"
          >
            {{ cat.charAt(0).toUpperCase() + cat.slice(1) }}
          </option>
        </select>

        <select v-model="selectedSort" class="dropdown">
          <option v-for="opt in sortOptions" :key="opt.value" :value="opt.value">
            {{ opt.label }}
          </option>
        </select>

        <button class="reset-btn" @click="resetFilters">Reset</button>
      </div>
    </div>

    <!-- Loading -->
    <div v-if="loading" class="loading">Sedang memuat artikel...</div>

    <!-- Empty -->
    <div v-else-if="empty" class="empty">Tidak ada artikel ditemukan.</div>

    <!-- Artikel -->
    <NuxtLink
      v-else
      v-for="article in articles"
      :key="article.id"
      :to="`/article/${article.id}`"
      class="article-link"
    >
      <ArticleCard
        :title="article.judul"
        :content="article.konten"
        :category="article.kategori"
        :date="article.tanggal"
      />
    </NuxtLink>

    <!-- Back to top -->
    <transition name="fade">
      <button v-if="showBackTop" class="back-top-btn" @click="scrollToTop">
        ↑ Kembali ke Atas
      </button>
    </transition>
  </div>
</template>

<style scoped>
.page-wrapper {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.header-section {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.page-title {
  font-size: 20px;
  font-weight: bold;
  color: var(--color-text);
}

.search-box {
  padding: 10px;
  font-size: 14px;
  border: 1px solid var(--color-border, #ccc);
  border-radius: 8px;
  background-color: white;
  color: var(--color-text);
}

.filter-bar {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: center;
}

.dropdown {
  flex: 1;
  min-width: 120px;
  padding: 10px;
  font-size: 14px;
  border: 1px solid var(--color-border, #ccc);
  border-radius: 8px;
  background-color: white;
  color: var(--color-text);
}

.reset-btn {
  padding: 10px 14px;
  font-size: 14px;
  background-color: #f3f3f3;
  border: 1px solid #ccc;
  color: #333;
  border-radius: 8px;
  cursor: pointer;
}

.reset-btn:hover {
  background-color: #e5e5e5;
}

.article-link {
  text-decoration: none;
  color: inherit;
}

.loading,
.empty {
  text-align: center;
  color: var(--color-text-light);
  margin-top: 48px;
  font-size: 14px;
}

.back-top-btn {
  position: fixed;
  bottom: 65px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 14px;
  color: var(--color-primary, #2c7be5);
  background: white;
  padding: 8px 14px;
  border: 1px solid #ddd;
  border-radius: 999px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  cursor: pointer;
  z-index: 10;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
