<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import ArticleCard from '~/components/ArticleCard.vue'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const router = useRouter()

const now = new Date()

const balance = ref(0)
const fetchSaldo = async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await $fetch('http://localhost:8080/users/me', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    balance.value = res.saldo || 0
  } catch (err) {
    console.error('Gagal mengambil saldo:', err)
    balance.value = 0
  }
}

const recommendedArticle = ref(null)
const fetchArticle = async () => {
  try {
    const res = await $fetch('http://localhost:8080/articles/', {
      method: 'GET',
      params: { page: 1, limit: 1 }
    })
    const data = res?.data || res || []
    recommendedArticle.value = data.length > 0 ? data[0] : null
  } catch (err) {
    console.error('Gagal mengambil artikel:', err)
    recommendedArticle.value = null
  }
}

const budgets = ref([])
const fetchBudgets = async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await $fetch('http://localhost:8080/budgets/', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })
    const data = res?.data || res || []

    budgets.value = data.slice(-2).map(b => ({
      id: b.id,
      name: b.nama || 'Tanpa nama',
      type: b.jenis_anggaran === 'primer' ? 'primary' : 'non-primary',
      income: b.pemasukan || 0,
      expense: b.pengeluaran || 0,
      start: b.tanggal.split('T')[0],
      end: b.tanggal.split('T')[0]
    }))
  } catch (err) {
    console.error('Gagal mengambil anggaran:', err)
    budgets.value = []
  }
}

onMounted(() => {
  fetchSaldo()
  fetchArticle()
  fetchBudgets()
})
</script>

<template>
  <div class="page-wrapper">
    <!-- SALDO -->
    <div class="card">
      <div class="label">Saldo</div>
      <div class="value">IDR {{ balance.toLocaleString('id-ID') }}</div>
    </div>

    <!-- ANGGARAN -->
    <div class="card">
      <div class="section-header">
        <div class="label">Daftar Anggaran</div>
      </div>
      <template v-if="budgets.length > 0">
        <div
          v-for="b in budgets"
          :key="b.id"
          class="budget-item"
        >
          <div class="budget-title">{{ b.name }}</div>
          <div class="budget-meta">
            <span>{{ b.start }} – {{ b.end }}</span>
            <span :class="{ over: b.expense > b.income }">
              {{ b.expense > b.income ? 'Overbudget' : 'Sisa ' + (b.income - b.expense).toLocaleString('id-ID') }}
            </span>
          </div>
        </div>
        <div class="see-more-wrapper">
          <button class="see-more-btn" @click="router.push('/budget')">
            Lihat semua anggaran →
          </button>
        </div>
      </template>
      <div v-else class="empty-state">Belum ada anggaran bulan ini.</div>
    </div>

    <!-- WARNING -->
    <div
      class="warning-box"
      v-if="budgets.some(b => b.expense > b.income)"
    >
      ⚠️ Beberapa anggaran kamu melebihi batas!
    </div>

    <!-- ARTIKEL -->
    <div>
      <div class="label">Rekomendasi Artikel</div>
      <template v-if="recommendedArticle">
        <NuxtLink :to="`/article/${recommendedArticle.id}`">
          <ArticleCard
            :title="recommendedArticle.judul"
            :content="recommendedArticle.konten"
            :category="recommendedArticle.kategori"
            :date="recommendedArticle.tanggal"
          />
        </NuxtLink>
      </template>
      <div v-else class="empty-state">Tidak ada artikel tersedia.</div>
    </div>
  </div>
</template>


<style scoped>
.page-wrapper {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.card {
  background: white;
  border-radius: 12px;
  padding: 16px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
}

.chart-header,
.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.filter-group {
  display: flex;
  gap: 8px;
}

.filter-group select {
  padding: 6px 8px;
  font-size: 13px;
  border: 1px solid #ccc;
  border-radius: 6px;
  background: #fff;
  color: #333;
}

.label {
  font-size: 14px;
  color: var(--color-text-light);
  margin-bottom: 6px;
}

.add-link {
  font-size: 14px;
  color: #2c7be5;
  cursor: pointer;
  font-weight: bold;
}

.value {
  font-size: 20px;
  font-weight: bold;
  color: var(--color-text);
}

.title {
  font-size: 16px;
  font-weight: 600;
  color: var(--color-text);
}

.content {
  font-size: 14px;
  color: var(--color-text-light);
  margin-top: 6px;
}

.meta {
  font-size: 12px;
  color: gray;
  margin-top: 10px;
}

.warning-box {
  background-color: #fff8e1;
  color: #ff6f00;
  padding: 12px;
  font-size: 14px;
  border-radius: 8px;
  text-align: center;
  font-weight: 500;
}

.budget-item {
  margin-top: 8px;
  padding: 6px 0;
  border-top: 1px dashed #eee;
}

.budget-title {
  font-weight: bold;
}

.budget-meta {
  display: flex;
  justify-content: space-between;
  font-size: 13px;
  color: #555;
}

.budget-meta .over {
  color: #d32f2f;
  font-weight: bold;
}

.trans-item {
  border-top: 1px dashed #eee;
  padding-top: 8px;
  margin-top: 8px;
  font-size: 14px;
}

.trans-info {
  display: flex;
  justify-content: space-between;
  font-weight: 500;
}

.trans-info .income {
  color: #2e7d32;
}

.trans-info .expense {
  color: #c62828;
}

.trans-date {
  font-size: 12px;
  color: #888;
  margin-top: 2px;
}

.empty-state {
  color: #888;
  font-size: 14px;
  margin-top: 8px;
}

.see-more-wrapper {
  margin-top: 12px;
  text-align: right;
}

.see-more-btn {
  font-size: 13px;
  background: none;
  border: none;
  color: #2c7be5;
  font-weight: bold;
  cursor: pointer;
  padding: 0;
}
</style>
