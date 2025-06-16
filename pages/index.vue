<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import BarChart from '~/components/BarChart.vue'

definePageMeta({ layout: 'logged-in' })

const router = useRouter()
const month = ref(new Date().getMonth())
const year = ref(new Date().getFullYear())
const months = [
  'Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni',
  'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'
]
const years = [2023, 2024, 2025]

// Dummy Transactions
const transactions = [
  { id: 1, type: 'income', amount: 3000000, category: 'Gaji', note: '', date: '2025-06-01' },
  { id: 2, type: 'expense', amount: 50000, category: 'Makan', note: '', date: '2025-06-02' },
  { id: 3, type: 'expense', amount: 1200000, category: 'Kos', note: '', date: '2025-06-03' },
  { id: 4, type: 'expense', amount: 20000, category: 'Ngopi', note: '', date: '2025-06-04' },
  { id: 5, type: 'income', amount: 250000, category: 'Freelance', note: '', date: '2025-06-06' },
]

// Dummy Budgets
const budgets = [
  { id: 1, name: 'Kos Bulanan', type: 'primary', income: 2000000, expense: 1800000, start: '2025-06-01', end: '2025-06-30' },
  { id: 2, name: 'Hiburan', type: 'non-primary', income: 500000, expense: 600000, start: '2025-06-10', end: '2025-06-20' }
]

// Dummy Article
const article = {
  title: 'Cara Mengatur Uang di Akhir Bulan',
  content: 'Tips cerdas agar uang kamu tidak habis di tanggal tua.',
  date: '2025-06-15',
  category: 'finance'
}

const balance = computed(() => {
  const current = transactions.filter(t => {
    const d = new Date(t.date)
    return d.getMonth() === month.value && d.getFullYear() === year.value
  })
  return current.reduce((total, t) => t.type === 'income' ? total + t.amount : total - t.amount, 0)
})

const recentTransactions = computed(() => {
  return transactions
    .filter(t => {
      const d = new Date(t.date)
      return d.getMonth() === month.value && d.getFullYear() === year.value
    })
    .sort((a, b) => new Date(b.date) - new Date(a.date))
    .slice(0, 3)
})

const activeBudgets = computed(() => {
  const today = new Date()
  return budgets.filter(b => new Date(b.start) <= today && new Date(b.end) >= today)
})

const toAddBudget = () => {
  router.push('/budget/add')
}
</script>

<template>
  <div class="page-wrapper">
    <!-- Balance -->
    <div class="card">
      <div class="label">Balance</div>
      <div class="value">IDR {{ balance.toLocaleString('id-ID') }}</div>
    </div>

    <!-- Anggaran Section -->
    <div class="card">
      <div class="section-header">
        <div class="label">Daftar Anggaran</div>
      </div>
      <div v-if="activeBudgets.length === 0" class="empty-state">
        Belum ada anggaran bulan ini.
      </div>
      <div v-else>
        <div v-for="b in activeBudgets" :key="b.id" class="budget-item">
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
      </div>
    </div>
    
    <!-- Warning -->
    <div class="warning-box" v-if="activeBudgets.some(b => b.expense > b.income)">
      ⚠️ Beberapa anggaran kamu melebihi batas!
    </div>

    <!-- Recent Transactions -->
    <div class="card">
      <div class="label">Transaksi Terbaru</div>
      <div v-for="t in recentTransactions" :key="t.id" class="trans-item">
        <div class="trans-info">
          <span :class="t.type">{{ t.type === 'income' ? '+' : '-' }} Rp{{ t.amount.toLocaleString('id-ID') }}</span>
          <span>{{ t.category }}</span>
        </div>
        <div class="trans-date">{{ t.date }}</div>
      </div>
    </div>

    <!-- Chart -->
    <div class="card">
      <div class="chart-header">
        <div class="label">Income vs Expenses</div>
        <div class="filter-group">
          <select v-model="month">
            <option v-for="(m, i) in months" :key="i" :value="i">{{ m }}</option>
          </select>
          <select v-model="year">
            <option v-for="y in years" :key="y" :value="y">{{ y }}</option>
          </select>
        </div>
      </div>
      <BarChart :month="month" :year="year" />
    </div>

    <!-- Article -->
    <div>
      <div class="label">Rekomendasi Artikel</div>
      <NuxtLink
        to="/article/1"
      >
        <ArticleCard
          :title="article.title"
          :content="article.content"
          :category="article.category"
          :date="article.date"
        />
      </NuxtLink>
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
