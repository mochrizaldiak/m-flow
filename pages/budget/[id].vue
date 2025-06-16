<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const route = useRoute()
const router = useRouter()

const budget = ref(null)
const transactions = ref([])

const dummyBudgets = [
  {
    id: 1,
    name: 'Kos Bulanan',
    type: 'primary',
    start: '2025-06-01',
    end: '2025-06-30',
    description: 'Biaya tempat tinggal bulan Juni'
  },
  {
    id: 2,
    name: 'Hiburan',
    type: 'non-primary',
    start: '2025-06-10',
    end: '2025-06-20',
    description: 'Langganan streaming dan bioskop'
  }
]

const dummyTransactions = [
  {
    id: 1,
    type: 'expense',
    amount: 1500000,
    category: 'Bayar Kos',
    budgetId: 1,
    date: '2025-06-03'
  },
  {
    id: 2,
    type: 'income',
    amount: 2000000,
    category: 'Transfer Orang Tua',
    budgetId: 1,
    date: '2025-06-02'
  },
  {
    id: 3,
    type: 'expense',
    amount: 120000,
    category: 'Netflix',
    budgetId: 2,
    date: '2025-06-11'
  }
]

const incomeTotal = computed(() =>
  relatedTransactions.value
    .filter(t => t.type === 'income')
    .reduce((sum, t) => sum + t.amount, 0)
)

const expenseTotal = computed(() =>
  relatedTransactions.value
    .filter(t => t.type === 'expense')
    .reduce((sum, t) => sum + t.amount, 0)
)

const relatedTransactions = computed(() =>
  transactions.value.filter(t => t.budgetId === budget.value?.id)
)

const status = computed(() => {
  const now = new Date()
  const end = new Date(budget.value?.end)
  if (expenseTotal.value > incomeTotal.value) return 'Overbudget'
  if (now > end) return 'Selesai'
  return 'Aktif'
})

onMounted(() => {
  const found = dummyBudgets.find(b => b.id === Number(route.params.id))
  if (!found) {
    alert('Anggaran tidak ditemukan.')
    router.push('/budget')
    return
  }

  budget.value = found
  transactions.value = dummyTransactions
})

const handleEdit = () => {
  router.push(`/budget/edit/${budget.value.id}`)
}

const handleDelete = () => {
  alert('🔧 Simulasi: anggaran dihapus.')
  router.push('/budget')
}
</script>

<template>
  <div v-if="budget" class="page-wrapper">
    <h2 class="page-title">{{ budget.name }}</h2>

    <div class="status-badge" :class="status.toLowerCase()">{{ status }}</div>

    <div class="detail-group">
      <label>Jenis</label>
      <p>{{ budget.type === 'primary' ? 'Primer' : 'Non-primer' }}</p>
    </div>

    <div class="detail-group">
      <label>Periode</label>
      <p>{{ budget.start }} → {{ budget.end }}</p>
    </div>

    <div v-if="budget.description" class="detail-group">
      <label>Deskripsi</label>
      <p>{{ budget.description }}</p>
    </div>

    <div class="detail-group">
      <label>Total Pemasukan</label>
      <p class="text-green">Rp {{ incomeTotal.toLocaleString('id-ID') }}</p>
    </div>

    <div class="detail-group">
      <label>Total Pengeluaran</label>
      <p class="text-red">Rp {{ expenseTotal.toLocaleString('id-ID') }}</p>
    </div>

    <div class="detail-group">
      <label>Selisih</label>
      <p :class="incomeTotal - expenseTotal >= 0 ? 'text-green' : 'text-red'">
        Rp {{ (incomeTotal - expenseTotal).toLocaleString('id-ID') }}
      </p>
    </div>

    <div class="button-group">
      <button class="btn edit" @click="handleEdit">✏️ Edit</button>
      <button class="btn delete" @click="handleDelete">🗑️ Hapus</button>
    </div>

    <hr class="divider" />

    <div class="transactions-section">
      <h3 class="section-title">Transaksi Terkait</h3>
      <div v-if="relatedTransactions.length === 0" class="empty-state">
        Belum ada transaksi yang tercatat untuk anggaran ini.
      </div>
      <div v-else class="transaction-list">
        <div v-for="t in relatedTransactions" :key="t.id" class="trans-item">
          <div class="top">
            <span :class="t.type === 'income' ? 'text-green' : 'text-red'">
              {{ t.type === 'income' ? '+' : '-' }}Rp {{ t.amount.toLocaleString('id-ID') }}
            </span>
            <span class="category">{{ t.category }}</span>
          </div>
          <div class="date">{{ t.date }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.page-wrapper {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.page-title {
  font-size: 20px;
  font-weight: bold;
}

.status-badge {
  padding: 4px 10px;
  font-size: 12px;
  font-weight: bold;
  border-radius: 999px;
  width: fit-content;
}

.status-badge.aktif {
  background-color: #e0f3ff;
  color: #2271b1;
}

.status-badge.selesai {
  background-color: #e9fbe5;
  color: #2e7d32;
}

.status-badge.overbudget {
  background-color: #ffe4e4;
  color: #c62828;
}

.detail-group label {
  font-size: 13px;
  font-weight: bold;
  color: #666;
}

.detail-group p {
  font-size: 14px;
  margin-top: 2px;
  margin-bottom: 10px;
}

.text-green {
  color: #2e7d32;
  font-weight: 500;
}

.text-red {
  color: #c62828;
  font-weight: 500;
}

.button-group {
  display: flex;
  gap: 12px;
  margin-top: 8px;
}

.btn {
  padding: 10px 16px;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  font-size: 14px;
  cursor: pointer;
}

.btn.edit {
  background-color: #2c7be5;
  color: white;
}

.btn.delete {
  background-color: #f44336;
  color: white;
}

.section-title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 6px;
}

.transaction-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.trans-item {
  padding: 10px 12px;
  border: 1px solid #eee;
  border-radius: 8px;
  background: #fafafa;
}

.top {
  display: flex;
  justify-content: space-between;
  font-size: 14px;
  font-weight: 500;
}

.category {
  color: #555;
}

.date {
  font-size: 12px;
  color: #888;
  margin-top: 4px;
}

.empty-state {
  font-size: 14px;
  color: #888;
}

.divider {
  margin-top: 24px;
  margin-bottom: 8px;
  border: none;
  border-top: 1px solid #eee;
}
</style>
