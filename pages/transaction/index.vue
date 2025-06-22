<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import TransactionCard from '~/components/TransactionCard.vue'

definePageMeta({ layout: 'logged-in' })

const router = useRouter()
const transaksiList = ref([])
const loading = ref(false)
const error = ref(false)

const loadTransaksi = async () => {
  try {
    loading.value = true
    error.value = false

    const token = localStorage.getItem('token')

    const res = await $fetch('http://localhost:8080/transactions/', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    transaksiList.value = (res?.data || res || []).map(item => ({
      id: item.id,
      type: item.jenis === 'pemasukan' ? 'income' : 'expense',
      amount: item.nominal,
      category: item.kategori,
      date: new Date(item.tanggal).toLocaleDateString('id-ID', {
        year: 'numeric', month: 'long', day: 'numeric'
      }),
      budgetName: item.budget.deskripsi || '-'
    }))
  } catch (err) {
    console.error('Gagal fetch transaksi:', err)
    error.value = true
  } finally {
    loading.value = false
  }
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

    <div v-if="loading" class="loading-state">Memuat transaksi...</div>
    <div v-else-if="error" class="error-state">Gagal memuat transaksi.</div>
    <div v-else-if="transaksiList.length === 0" class="empty-state">Belum ada transaksi.</div>

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

.empty-state,
.loading-state,
.error-state {
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
