<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const router = useRouter()

// Form data
const amount = ref('')
const type = ref('expense')
const category = ref('')
const note = ref('')
const date = ref('')
const selectedBudget = ref('')

// Dummy budget list
const dummyBudgets = [
  { id: 1, name: 'Kos Bulanan' },
  { id: 2, name: 'Makan Harian' },
  { id: 3, name: 'Hiburan' }
]

// Validasi form
const isValid = computed(() =>
  amount.value && category.value && date.value && selectedBudget.value
)

// Simpan transaksi (dummy)
const saveTransaction = async () => {
  const payload = {
    type: type.value,
    amount: Number(amount.value),
    category: category.value,
    note: note.value,
    date: date.value,
    budgetId: selectedBudget.value,
    createdAt: new Date().toISOString()
  }

  console.log('📦 Transaksi siap dikirim:', payload)

  alert('✅ Transaksi berhasil disiapkan!')
  router.push('/transaction')
}
</script>

<template>
  <div class="page-wrapper">
    <h2 class="page-title">Tambah Transaksi</h2>

    <div class="form-group">
      <label>Nominal</label>
      <input v-model="amount" type="number" class="input" placeholder="Contoh: 100000" />
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
      <input v-model="category" type="text" class="input" placeholder="Contoh: Gaji, Makan Siang" />
    </div>

    <div class="form-group">
      <label>Tanggal</label>
      <input v-model="date" type="date" class="input" />
    </div>

    <div class="form-group">
      <label>Masukkan ke Anggaran</label>
      <select v-model="selectedBudget" class="input">
        <option disabled value="">Pilih anggaran</option>
        <option v-for="b in dummyBudgets" :key="b.id" :value="b.name">
          {{ b.name }}
        </option>
      </select>
    </div>

    <div class="form-group">
      <label>Catatan (Opsional)</label>
      <textarea v-model="note" class="input" rows="3" />
    </div>

    <button class="btn full-width" :disabled="!isValid" @click="saveTransaction">
      SIMPAN
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
