<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const router = useRouter()

const search = ref('')
const filter = ref('all')
const budgets = ref([
  {
    id: 1,
    name: 'Kos Bulanan',
    type: 'primary',
    income: 2000000,
    expense: 1500000,
    start: '2025-06-01',
    end: '2025-06-30',
    description: 'Biaya tempat tinggal bulan Juni'
  },
  {
    id: 2,
    name: 'Hiburan',
    type: 'non-primary',
    income: 1000000,
    expense: 1200000,
    start: '2025-06-10',
    end: '2025-06-20',
    description: 'Langganan streaming dan bioskop'
  }
])

const filteredBudgets = computed(() => {
  return budgets.value.filter(b => {
    const matchesSearch = b.name.toLowerCase().includes(search.value.toLowerCase())
    const matchesFilter = filter.value === 'all' || b.type === filter.value
    return matchesSearch && matchesFilter
  })
})

const toAddBudget = () => {
  router.push('/budget/add')
}
</script>

<template>
  <div class="page-wrapper">
    <div class="header-bar">
      <h2 class="page-title">Daftar Anggaran</h2>
      <button class="btn add-btn" @click="toAddBudget">+ Tambah</button>
    </div>

    <div class="filters">
      <input v-model="search" type="text" class="input" placeholder="Cari nama anggaran..." />
      <select v-model="filter" class="input">
        <option value="all">Semua</option>
        <option value="primary">Primer</option>
        <option value="non-primary">Non-primer</option>
      </select>
    </div>

    <div v-if="filteredBudgets.length === 0" class="empty-state">
      Tidak ada anggaran ditemukan.
    </div>

    <div class="budget-list">
      <BudgetCard
        v-for="b in filteredBudgets"
        :key="b.id"
        :id="b.id"
        :name="b.name"
        :type="b.type"
        :income="b.income"
        :expense="b.expense"
        :start="b.start"
        :end="b.end"
      />
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

.page-title {
  font-size: 20px;
  font-weight: bold;
  color: var(--color-text);
}

.header-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.add-btn {
  background-color: #2c7be5;
  color: white;
  padding: 8px 16px;
  font-size: 14px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
}

.filters {
  display: flex;
  gap: 8px;
}

.input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
  flex: 1;
}

.budget-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.empty-state {
  color: #888;
  text-align: center;
  font-size: 14px;
  padding: 24px 0;
}
</style>