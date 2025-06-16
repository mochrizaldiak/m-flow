<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const route = useRoute()
const router = useRouter()

const id = ref(null)
const name = ref('')
const type = ref('primary')
const start = ref('')
const end = ref('')
const description = ref('')

const isValid = computed(() =>
  name.value && start.value && end.value
)

const dummyBudgets = [
  {
    id: 1,
    name: 'Kos Bulanan',
    type: 'primary',
    start: '2025-06-01',
    end: '2025-06-30',
    description: 'Biaya tempat tinggal bulan Juni'
  },
  {
    id: 2,
    name: 'Hiburan',
    type: 'non-primary',
    start: '2025-06-10',
    end: '2025-06-20',
    description: 'Langganan streaming dan bioskop'
  }
]

const loadBudget = () => {
  const target = dummyBudgets.find(b => b.id === Number(route.params.id))
  if (!target) {
    alert('Anggaran tidak ditemukan.')
    router.push('/budget')
    return
  }

  id.value = target.id
  name.value = target.name
  type.value = target.type
  start.value = target.start
  end.value = target.end
  description.value = target.description || ''
}

const updateBudget = () => {
  const payload = {
    id: id.value,
    name: name.value,
    type: type.value,
    start: start.value,
    end: end.value,
    description: description.value,
    updatedAt: new Date().toISOString()
  }

  console.log('📦 Data update anggaran:', payload)
  alert('✅ Perubahan anggaran berhasil disimpan (dummy).')
  router.push(`/budget/${id.value}`)
}

onMounted(loadBudget)
</script>

<template>
  <div class="page-wrapper">
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
