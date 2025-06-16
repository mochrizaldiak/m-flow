<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const router = useRouter()

const name = ref('')
const type = ref('primary')
const start = ref('')
const end = ref('')
const description = ref('')

const isValid = computed(() =>
  name.value && start.value && end.value
)

const saveBudget = () => {
  const payload = {
    name: name.value,
    type: type.value,
    start: start.value,
    end: end.value,
    description: description.value,
    createdAt: new Date().toISOString()
  }

  console.log('📦 Anggaran baru:', payload)
  alert('✅ Anggaran berhasil disiapkan!')
  router.push('/budget')
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
        <label><input type="radio" value="primary" v-model="type" /> Primer</label>
        <label><input type="radio" value="non-primary" v-model="type" /> Non-primer</label>
      </div>
    </div>

    <div class="form-group">
      <label>Tanggal Mulai</label>
      <input v-model="start" type="date" class="input" />
    </div>

    <div class="form-group">
      <label>Tanggal Selesai</label>
      <input v-model="end" type="date" class="input" />
    </div>

    <div class="form-group">
      <label>Deskripsi (Opsional)</label>
      <textarea v-model="description" class="input" rows="3" />
    </div>

    <button class="btn full-width" :disabled="!isValid" @click="saveBudget">
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
