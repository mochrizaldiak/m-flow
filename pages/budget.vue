<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { openDB } from 'idb'

definePageMeta({ layout: 'logged-in' })

const allocationName = ref('')
const budgetType = ref('primary')
const income = ref('')
const expense = ref('')
const startDate = ref('')
const endDate = ref('')

const router = useRouter()

const saveBudget = async () => {
  const db = await openDB('mflow-db', 1, {
    upgrade(db) {
      if (!db.objectStoreNames.contains('anggaran')) {
        db.createObjectStore('anggaran', { keyPath: 'id', autoIncrement: true })
      }
    }
  })

  await db.add('anggaran', {
    name: allocationName.value,
    type: budgetType.value,
    income: Number(income.value),
    expense: Number(expense.value),
    start: startDate.value,
    end: endDate.value,
    createdAt: new Date().toISOString()
  })

  alert('✅ Budget berhasil disimpan!')
  router.push('/anggaran')
}
</script>

<template>
  <div class="page-wrapper">
    <h2 class="page-title">Tambah Anggaran</h2>

    <div class="form-group">
      <label>Nama Alokasi</label>
      <input v-model="allocationName" type="text" class="input" />
    </div>

    <div class="form-group">
      <label>Jenis Anggaran</label>
      <div class="radio-group">
        <label><input type="radio" value="primary" v-model="budgetType" /> Primer</label>
        <label><input type="radio" value="non-primary" v-model="budgetType" /> Non-primer</label>
      </div>
    </div>

    <div class="form-group">
      <label>Income</label>
      <input v-model="income" type="number" class="input" />
    </div>

    <div class="form-group">
      <label>Expense</label>
      <input v-model="expense" type="number" class="input" />
    </div>

    <div class="form-group">
      <label>Tanggal Mulai</label>
      <input v-model="startDate" type="date" class="input" />
    </div>

    <div class="form-group">
      <label>Tanggal Selesai</label>
      <input v-model="endDate" type="date" class="input" />
    </div>

    <button class="btn full-width" @click="saveBudget">SIMPAN</button>
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
  margin-bottom: 8px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.radio-group {
  display: flex;
  gap: 16px;
  margin-top: 6px;
  font-size: 14px;
  color: var(--color-text);
}

.full-width {
  width: 100%;
  margin-top: 16px;
}
</style>
