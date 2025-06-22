<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const route = useRoute()
const router = useRouter()

const id = ref(null)
const name = ref('')
const type = ref('primary')
const start = ref('')
const end = ref('')
const description = ref('')
const loading = ref(true)

const isValid = computed(() =>
  name.value && start.value && end.value
)

const loadBudget = async () => {
  try {
    loading.value = true
    const token = localStorage.getItem('token')
    const res = await $fetch(`http://localhost:8080/budgets/${route.params.id}`, {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    id.value = res.id
    name.value = res.nama
    type.value = res.jenis_anggaran === 'primer' ? 'primary' : 'non-primary'
    start.value = res.tanggal?.split('T')[0] || ''
    end.value = res.tanggal_akhir?.split('T')[0] || ''
    description.value = res.deskripsi || ''
  } catch (err) {
    console.error('Gagal mengambil anggaran:', err)
    alert('Gagal mengambil data anggaran.')
    router.push('/budget')
  } finally {
    loading.value = false
  }
}

const updateBudget = async () => {
  try {
    const token = localStorage.getItem('token')
    const payload = {
      nama: name.value,
      jenis_anggaran: type.value === 'primary' ? 'primer' : 'non-primer',
      tanggal: start.value,
      tanggal_akhir: end.value,
      deskripsi: description.value
    }

    await $fetch(`http://localhost:8080/budgets/${id.value}`, {
      method: 'PUT',
      headers: {
        Authorization: `Bearer ${token}`
      },
      body: payload
    })

    alert('✅ Anggaran berhasil diperbarui.')
    router.push(`/budget/${id.value}`)
  } catch (err) {
    console.error('Gagal update:', err)
    alert('Gagal memperbarui anggaran.')
  }
}

onMounted(loadBudget)
</script>

<template>
  <div class="page-wrapper" v-if="!loading">
    <h2 class="page-title">Edit Anggaran</h2>

    <div class="form-group">
      <label>Nama Anggaran</label>
      <input v-model="name" type="text" class="input" />
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

    <button class="btn full-width" :disabled="!isValid" @click="updateBudget">
      SIMPAN PERUBAHAN
    </button>
  </div>

  <div v-else class="loading">Memuat data anggaran...</div>
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

.loading {
  text-align: center;
  padding: 48px 0;
  color: #888;
  font-size: 14px;
}
</style>
