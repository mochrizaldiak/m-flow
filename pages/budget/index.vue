<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import BudgetCard from '~/components/BudgetCard.vue'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const router = useRouter()
const search = ref('')
const filter = ref('all')
const budgets = ref([])
const loading = ref(true)
const error = ref(false)

const fetchBudgets = async () => {
  try {
    loading.value = true
    const token = localStorage.getItem('token')

    const res = await $fetch('http://localhost:8080/budgets/', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    budgets.value = res.data || res || []
  } catch (err) {
    console.error('Gagal mengambil data anggaran:', err)
    error.value = true
  } finally {
    loading.value = false
  }
}

onMounted(fetchBudgets)

const filteredBudgets = computed(() => {
  return budgets.value.filter(b => {
    const matchesSearch = b.deskripsi?.toLowerCase().includes(search.value.toLowerCase())
    const matchesFilter = filter.value === 'all' || b.jenis_anggaran === filter.value
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
        <option value="primer">Primer</option>
        <option value="non-primer">Non-primer</option>
      </select>
    </div>

    <div v-if="loading" class="empty-state">Memuat data anggaran...</div>
    <div v-else-if="error" class="empty-state">Gagal memuat data.</div>
    <div v-else-if="filteredBudgets.length === 0" class="empty-state">
      Tidak ada anggaran ditemukan.
    </div>

    <div class="budget-list">
      <BudgetCard
        v-for="b in filteredBudgets"
        :key="b.id"
        :id="b.id"
        :name="b.nama"
        :type="b.jenis_anggaran"
        :income="b.pemasukan"
        :expense="b.pengeluaran"
        :start="new Date(b.tanggal).toISOString().split('T')[0]"
        :end="new Date(b.tanggal).toISOString().split('T')[0]"
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
