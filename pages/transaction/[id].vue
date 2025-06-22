<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const route = useRoute()
const router = useRouter()
const transaction = ref(null)
const loading = ref(true)

const loadTransaction = async () => {
  try {
    const id = route.params.id
    const token = localStorage.getItem('token')

    const res = await $fetch(`http://localhost:8080/transactions/${id}`, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    transaction.value = {
      id: res.id,
      type: res.jenis === 'pemasukan' ? 'income' : 'expense',
      amount: res.nominal,
      category: res.kategori,
      date: new Date(res.tanggal).toLocaleDateString('id-ID', {
        year: 'numeric', month: 'long', day: 'numeric'
      }),
      note: res.catatan || '-',
      budgetName: res.budget?.deskripsi || '-'
    }
  } catch (err) {
    console.error('Gagal mengambil transaksi:', err)
    alert('Transaksi tidak ditemukan.')
    router.push('/transaction')
  } finally {
    loading.value = false
  }
}

const handleDelete = async () => {
  const confirmDelete = confirm('Apakah kamu yakin ingin menghapus transaksi ini?')

  if (!confirmDelete) return

  try {
    const token = localStorage.getItem('token')
    await $fetch(`http://localhost:8080/transactions/${transaction.value.id}`, {
      method: 'DELETE',
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    alert('✅ Transaksi berhasil dihapus.')
    router.push('/transaction')
  } catch (err) {
    console.error('Gagal menghapus transaksi:', err)
    alert('❌ Gagal menghapus transaksi.')
  }
}

const handleEdit = () => {
  router.push(`/transaction/edit/${transaction.value.id}`)
}

onMounted(loadTransaction)
</script>

<template>
  <div class="page-wrapper" v-if="transaction">
    <h2 class="page-title">{{ transaction.category }}</h2>

    <div class="amount" :class="transaction.type">
      {{ transaction.type === 'income' ? '+' : '-' }}Rp {{ transaction.amount.toLocaleString('id-ID') }}
    </div>

    <div class="status-badge" :class="transaction.type">
      {{ transaction.type === 'income' ? 'Pemasukan' : 'Pengeluaran' }}
    </div>

    <div class="detail-group">
      <label>Tanggal</label>
      <p>{{ transaction.date }}</p>
    </div>

    <div class="detail-group">
      <label>Masuk ke Anggaran</label>
      <p>{{ transaction.budgetName }}</p>
    </div>

    <div class="detail-group" v-if="transaction.note">
      <label>Catatan</label>
      <p>{{ transaction.note }}</p>
    </div>

    <div class="button-group">
      <button class="btn edit" @click="handleEdit">✏️ Edit</button>
      <button class="btn delete" @click="handleDelete">🗑️ Hapus</button>
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

.amount {
  font-size: 24px;
  font-weight: bold;
}

.amount.income {
  color: #2e7d32;
}

.amount.expense {
  color: #c62828;
}

.status-badge {
  padding: 4px 10px;
  font-size: 12px;
  font-weight: bold;
  border-radius: 999px;
  display: inline-block;
  width: fit-content;
}

.status-badge.income {
  background-color: #e6f4ea;
  color: #2e7d32;
}

.status-badge.expense {
  background-color: #fdecea;
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
</style>
