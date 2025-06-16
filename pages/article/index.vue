<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
import ArticleCard from '~/components/ArticleCard.vue'

definePageMeta({ layout: 'logged-in' })

const searchQuery = ref('')
const selectedCategory = ref('all')
const selectedSort = ref('newest')
const showBackTop = ref(false)
const headerRef = ref(null)

const categories = ['all', 'finance', 'saving', 'budgeting']
const sortOptions = [
  { label: 'A → Z', value: 'az' },
  { label: 'Z → A', value: 'za' },
  { label: 'Terbaru', value: 'newest' },
  { label: 'Terlama', value: 'oldest' }
]

const resetFilters = () => {
  searchQuery.value = ''
  selectedCategory.value = 'all'
  selectedSort.value = 'newest'
}

const scrollToTop = () => {
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

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
  },
  {
    id: 3,
    title: 'Kenapa Budgeting Itu Penting?',
    content: 'Budgeting membantu kamu menghindari pemborosan dan mencapai tujuan keuangan...',
    category: 'budgeting',
    date: '2025-05-28'
  },
  {
    id: 4,
    title: 'Memahami Fixed vs Variable Expense',
    content: 'Pahami jenis pengeluaran agar bisa mengontrol cash flow dengan lebih baik...',
    category: 'finance',
    date: '2025-05-20'
  },
  {
    id: 5,
    title: '5 Langkah Membuat Dana Darurat',
    content: 'Dana darurat wajib dimiliki untuk menghadapi hal tak terduga...',
    category: 'saving',
    date: '2025-05-15'
  },
  {
    id: 6,
    title: 'Cara Mencatat Pengeluaran Harian',
    content: 'Catat pengeluaran setiap hari untuk kontrol penuh atas uangmu...',
    category: 'budgeting',
    date: '2025-05-10'
  },
  {
    id: 7,
    title: 'Apa Itu Financial Goal?',
    content: 'Menetapkan tujuan finansial membantumu lebih fokus dan terarah...',
    category: 'finance',
    date: '2025-05-05'
  },
  {
    id: 8,
    title: 'Tips Hidup Hemat di Kost',
    content: 'Buat kamu anak kos, ini dia tips hidup hemat tapi tetap nyaman...',
    category: 'saving',
    date: '2025-04-28'
  },
  {
    id: 9,
    title: 'Menggunakan Amplop untuk Budget',
    content: 'Metode amplop bisa membantumu membatasi pengeluaran per kategori...',
    category: 'budgeting',
    date: '2025-04-20'
  },
  {
    id: 10,
    title: 'Mengenal Aplikasi Keuangan Pribadi',
    content: 'Aplikasi seperti M-Flow bisa bantu atur keuangan kamu lebih praktis...',
    category: 'finance',
    date: '2025-04-15'
  }
]

const filteredArticles = computed(() => {
  let result = dummyArticles.filter((article) => {
    const matchCategory =
      selectedCategory.value === 'all' || article.category === selectedCategory.value
    const matchSearch =
      article.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      article.content.toLowerCase().includes(searchQuery.value.toLowerCase())
    return matchCategory && matchSearch
  })

  return result.sort((a, b) => {
    if (selectedSort.value === 'az') return a.title.localeCompare(b.title)
    if (selectedSort.value === 'za') return b.title.localeCompare(a.title)
    if (selectedSort.value === 'newest') return new Date(b.date) - new Date(a.date)
    if (selectedSort.value === 'oldest') return new Date(a.date) - new Date(b.date)
    return 0
  })
})

// Observer for showing back-to-top button
let observer
onMounted(() => {
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
          <option v-for="cat in categories.filter(c => c !== 'all')" :key="cat" :value="cat">
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

    <NuxtLink
      v-for="article in filteredArticles"
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
