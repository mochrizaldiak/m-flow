<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const route = useRoute()
const router = useRouter()

// Form fields
const amount = ref('')
const type = ref('expense')
const category = ref('')
const note = ref('')
const date = ref('')
const budgetName = ref('')
const id = ref(null)

// Dummy data transaksi
const dummyTransactions = [
  {
    id: 1,
    type: 'income',
    amount: 2000000,
    category: 'Gaji Bulanan',
    note: 'Gaji bulan Juni',
    date: '2025-06-01',
    budgetName: 'Kos Bulanan'
  },
  {
    id: 2,
    type: 'expense',
    amount: 30000,
    category: 'Makan Siang',
    note: 'Nasi padang',
    date: '2025-06-02',
    budgetName: 'Makan Harian'
  }
]

// Dummy list anggaran
const budgetOptions = ['Kos Bulanan', 'Makan Harian', 'Hiburan', 'Transportasi']

// Validasi
const isValid = computed(() =>
  amount.value && category.value && date.value && budgetName.value
)

// Load data transaksi
const loadTransaction = () => {
  const target = dummyTransactions.find(t => t.id === Number(route.params.id))

  if (!target) {
    alert('Transaksi tidak ditemukan.')
    router.push('/transaction')
    return
  }

  id.value = target.id
  amount.value = target.amount
  type.value = target.type
  category.value = target.category
  note.value = target.note || ''
  date.value = target.date
  budgetName.value = target.budgetName || ''
}

// Simulasi update
const updateTransaction = () => {
  const payload = {
    id: id.value,
    amount: Number(amount.value),
    type: type.value,
    category: category.value,
    note: note.value,
    date: date.value,
    budgetName: budgetName.value,
    updatedAt: new Date().toISOString()
  }

  console.log('📦 Update transaksi:', payload)
  alert('✅ Perubahan berhasil disimpan (simulasi)')
  router.push(`/transaction/${id.value}`)
}

onMounted(loadTransaction)
</script>

<template>
  <div class="page-wrapper">
    <h2 class="page-title">Edit Transaksi</h2>

    <div class="form-group">
      <label>Nominal</label>
      <input v-model="amount" type="number" class="input" />
    </div>

    <div class="form-group">
      <label>Jenis</label>
      <div class="radio-group">
        <label><input type="radio" value="income" v-model="type" /> Pemasukan</label>
        <label><input type="radio" value="expense" v-model="type" /> Pengeluaran</label>
      </div>
    </div>

    <div class="form-group">
      <label>Kategori</label>
      <input v-model="category" type="text" class="input" />
    </div>

    <div class="form-group">
      <label>Masuk ke Anggaran</label>
      <select v-model="budgetName" class="input">
        <option disabled value="">-- Pilih Anggaran --</option>
        <option v-for="b in budgetOptions" :key="b" :value="b">{{ b }}</option>
      </select>
    </div>

    <div class="form-group">
      <label>Tanggal</label>
      <input v-model="date" type="date" class="input" />
    </div>

    <div class="form-group">
      <label>Catatan (Opsional)</label>
      <textarea v-model="note" class="input" rows="3" />
    </div>

    <button class="btn full-width" :disabled="!isValid" @click="updateTransaction">
      SIMPAN PERUBAHAN
    </button>
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

.form-group {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
}

.radio-group {
  display: flex;
  gap: 16px;
  font-size: 14px;
  margin-top: 6px;
}

.btn {
  background-color: #2c7be5;
  color: white;
  padding: 12px;
  font-size: 15px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
}

.btn:disabled {
  background-color: #ccc;
  cursor: not-allowed;
  opacity: 0.7;
}

.full-width {
  width: 100%;
}
</style>
