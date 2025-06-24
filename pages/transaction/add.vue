<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const router = useRouter()

const amount = ref('')
const type = ref('expense')
const category = ref('')
const note = ref('')
const date = ref('')
const selectedBudget = ref('')
const budgets = ref([])
const loadingBudgets = ref(false)
const submitting = ref(false)

const isValid = computed(() =>
  amount.value && category.value && date.value && selectedBudget.value
)

const fetchBudgets = async () => {
  try {
    loadingBudgets.value = true
    const token = localStorage.getItem('token')

    const res = await $fetch('http://localhost:8080/budgets/', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    budgets.value = res?.data || res || []
  } catch (err) {
    console.error('Gagal memuat anggaran:', err)
    budgets.value = []
  } finally {
    loadingBudgets.value = false
  }
}

const saveTransaction = async () => {
  try {
    submitting.value = true

    const token = localStorage.getItem('token')

    const payload = {
      nominal: Number(amount.value),
      jenis: type.value,
      kategori: category.value,
      catatan: note.value,
      tanggal: new Date(date.value).toISOString(),
      budget_id: Number(selectedBudget.value)
    }

    await $fetch('http://localhost:8080/transactions/', {
      method: 'POST',
      body: payload,
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    alert('✅ Transaksi berhasil ditambahkan!')
    router.push('/transaction')
  } catch (err) {
    console.error('Gagal menyimpan transaksi:', err)
    alert('❌ Gagal menyimpan transaksi.')
  } finally {
    submitting.value = false
  }
}

onMounted(fetchBudgets)
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
        <label><input type="radio" value="pemasukan" v-model="type" /> Pemasukan</label>
        <label><input type="radio" value="pengeluaran" v-model="type" /> Pengeluaran</label>
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
        <option
          v-for="b in budgets"
          :key="b.id"
          :value="b.id"
        >
          {{ b.nama }}
        </option>
      </select>
    </div>

    <div class="form-group">
      <label>Catatan (Opsional)</label>
      <textarea v-model="note" class="input" rows="3" />
    </div>

    <button class="btn full-width" :disabled="!isValid || submitting" @click="saveTransaction">
      {{ submitting ? 'Menyimpan...' : 'SIMPAN' }}
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
