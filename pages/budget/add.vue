<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const router = useRouter()

const name = ref('')
const type = ref('primer')
const start = ref('')
const description = ref('')
const period = ref('M') // default Bulan
const loading = ref(false)

const isValid = computed(() =>
  name.value && start.value && period.value
)

const saveBudget = async () => {
  try {
    loading.value = true
    const token = localStorage.getItem('token')
    const payload = {
      nama: name.value,
      jenis_anggaran: type.value,
      tanggal_mulai: start.value,
      jenis_periode: period.value,
      deskripsi: description.value
    }

    await $fetch('http://localhost:8080/budgets/', {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${token}`
      },
      body: payload
    })

    alert('✅ Anggaran berhasil ditambahkan!')
    router.push('/budget')
  } catch (err) {
    console.error('Gagal menambahkan anggaran:', err)
    alert('❌ Gagal menambahkan anggaran.')
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="page-wrapper">
    <h2 class="page-title">Tambah Anggaran</h2>

    <div class="form-group">
      <label>Nama Alokasi</label>
      <input v-model="name" type="text" class="input" placeholder="Contoh: Kos Bulanan" />
    </div>

    <div class="form-group">
      <label>Jenis Anggaran</label>
      <div class="radio-group">
        <label><input type="radio" value="primer" v-model="type" /> Primer</label>
        <label><input type="radio" value="non-primer" v-model="type" /> Non-primer</label>
      </div>
    </div>

    <div class="form-group">
      <label>Tanggal Mulai</label>
      <input v-model="start" type="date" class="input" />
    </div>

    <div class="form-group">
      <label>Periode</label>
      <select v-model="period" class="input">
        <option value="D">Harian</option>
        <option value="W">Mingguan</option>
        <option value="M">Bulanan</option>
        <option value="Y">Tahunan</option>
      </select>
    </div>

    <div class="form-group">
      <label>Deskripsi (Opsional)</label>
      <textarea v-model="description" class="input" rows="3" />
    </div>

    <button
      class="btn full-width"
      :disabled="!isValid || loading"
      @click="saveBudget"
    >
      {{ loading ? 'Menyimpan...' : 'SIMPAN' }}
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
