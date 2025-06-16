<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import TransactionCard from '~/components/TransactionCard.vue'

definePageMeta({ layout: 'logged-in' })

const router = useRouter()
const transaksiList = ref([])

const dummyTransactions = [
  {
    id: 1,
    type: 'income',
    amount: 2000000,
    category: 'Gaji Bulanan',
    date: '2025-06-01',
    budgetName: 'Kos Bulanan'
  },
  {
    id: 2,
    type: 'expense',
    amount: 30000,
    category: 'Makan Siang',
    date: '2025-06-02',
    budgetName: 'Makan Harian'
  },
  {
    id: 3,
    type: 'expense',
    amount: 100000,
    category: 'Pulsa',
    date: '2025-06-03',
    budgetName: 'Komunikasi'
  },
  {
    id: 4,
    type: 'income',
    amount: 50000,
    category: 'Freelance',
    date: '2025-06-04',
    budgetName: 'Hiburan'
  }
]

const loadTransaksi = () => {
  transaksiList.value = dummyTransactions
}

const toTambahTransaksi = () => {
  router.push('/transaction/add')
}

onMounted(loadTransaksi)
</script>

<template>
  <div class="page-wrapper">
    <div class="header-bar">
      <h2 class="page-title">Transaksi</h2>
      <button class="btn add-btn" @click="toTambahTransaksi">+ Tambah</button>
    </div>

    <div v-if="transaksiList.length === 0" class="empty-state">
      Belum ada transaksi tercatat. Yuk mulai catat sekarang!
    </div>

    <div v-else class="transaksi-list">
      <TransactionCard
        v-for="item in transaksiList"
        :key="item.id"
        :id="item.id"
        :type="item.type"
        :amount="item.amount"
        :category="item.category"
        :date="item.date"
        :budgetName="item.budgetName"
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

.empty-state {
  text-align: center;
  color: #888;
  font-size: 14px;
  padding: 32px 0;
}

.transaksi-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}
</style>
