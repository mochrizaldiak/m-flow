<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const route = useRoute()
const router = useRouter()

// Form fields
const id = ref(null)
const amount = ref('')
const type = ref('expense')
const category = ref('')
const note = ref('')
const date = ref('')
const budgetId = ref('')
const budgets = ref([])
const loading = ref(true)
const saving = ref(false)

// Validasi
const isValid = computed(() =>
  amount.value && category.value && date.value && budgetId.value
)

// Load list anggaran
const loadBudgets = async () => {
  const token = localStorage.getItem('token')
  const res = await $fetch('http://localhost:8080/budgets/', {
    headers: { Authorization: `Bearer ${token}` }
  })
  budgets.value = res?.data || res
}

// Load data transaksi
const loadTransaction = async () => {
  try {
    const token = localStorage.getItem('token')
    const res = await $fetch(`http://localhost:8080/transactions/${route.params.id}`, {
      headers: { Authorization: `Bearer ${token}` }
    })

    id.value = res.id
    amount.value = res.nominal
    type.value = res.jenis === 'pemasukan' ? 'income' : 'expense'
    category.value = res.kategori
    note.value = res.catatan || ''
    date.value = res.tanggal.split('T')[0]
    budgetId.value = res.budget_id
  } catch (err) {
    alert('❌ Gagal memuat data transaksi.')
    router.push('/transaction')
  } finally {
    loading.value = false
  }
}

// Simpan perubahan
const updateTransaction = async () => {
  try {
    saving.value = true
    const token = localStorage.getItem('token')

    const payload = {
      nominal: Number(amount.value),
      jenis: type.value === 'income' ? 'pemasukan' : 'pengeluaran',
      kategori: category.value,
      catatan: note.value,
      tanggal: new Date(date.value).toISOString(),
      budget_id: Number(budgetId.value)
    }

    await $fetch(`http://localhost:8080/transactions/${id.value}`, {
      method: 'PUT',
      body: payload,
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    alert('✅ Transaksi berhasil diperbarui.')
    router.push(`/transaction/${id.value}`)
  } catch (err) {
    console.error(err)
    alert('❌ Gagal menyimpan perubahan.')
  } finally {
    saving.value = false
  }
}

onMounted(async () => {
  await loadBudgets()
  await loadTransaction()
})
</script>

<template>
  <div class="page-wrapper" v-if="!loading">
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
      <select v-model="budgetId" class="input">
        <option disabled value="">-- Pilih Anggaran --</option>
        <option v-for="b in budgets" :key="b.id" :value="b.id">
          {{ b.deskripsi }}
        </option>
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

    <button class="btn full-width" :disabled="!isValid || saving" @click="updateTransaction">
      {{ saving ? 'Menyimpan...' : 'SIMPAN PERUBAHAN' }}
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
