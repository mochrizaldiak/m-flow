<script setup>
import { ref, onMounted } from 'vue'
import { openDB } from 'idb'

definePageMeta({ layout: 'logged-in' })

const nama = ref('User M-Flow')
const skor = ref(75)
const status = ref('')
const rekomendasi = ref('')

const getStatus = (val) => {
  if (val >= 80) return 'Baik'
  if (val >= 60) return 'Cukup'
  return 'Perlu Diperbaiki'
}

const getRekomendasi = (val) => {
  if (val >= 80) return 'Pertahankan pengelolaan keuanganmu.'
  if (val >= 60) return 'Coba alokasikan pengeluaran lebih bijak.'
  return 'Perlu evaluasi besar terhadap keuanganmu.'
}

onMounted(async () => {
  const db = await openDB('mflow-db', 1)
  const session = await db.get('auth', 'session')
  if (session) {
    nama.value = 'Pengguna M-Flow'
  }

  status.value = getStatus(skor.value)
  rekomendasi.value = getRekomendasi(skor.value)
})
</script>

<template>
  <div class="profile-wrapper">
    <h2 class="profile-title">Profil</h2>
    <p class="profile-name">Halo, {{ nama }}</p>

    <div class="score-box">
      <div class="score-circle">{{ skor }}</div>
      <p class="score-status">Skor Keuangan: <strong>{{ status }}</strong></p>
    </div>

    <div class="recommendation-card">
      <h3 class="recommendation-title">Rekomendasi</h3>
      <p class="recommendation-text">{{ rekomendasi }}</p>
    </div>
  </div>
</template>

<style scoped>
.profile-wrapper {
  padding: 20px 16px;
  max-width: 375px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 24px;
  align-items: center;
}

.profile-title {
  font-size: 22px;
  font-weight: bold;
  color: var(--color-text);
  margin-bottom: 4px;
}

.profile-name {
  font-size: 14px;
  color: var(--color-text-light);
}

.score-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.score-circle {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background-color: var(--color-primary);
  color: white;
  font-size: 36px;
  font-weight: bold;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.score-status {
  font-size: 15px;
  color: var(--color-text);
}

.recommendation-card {
  background: var(--color-white);
  padding: 16px;
  border-radius: 12px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
  width: 100%;
  text-align: left;
}

.recommendation-title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 6px;
  color: var(--color-text);
}

.recommendation-text {
  font-size: 14px;
  color: var(--color-text-light);
  line-height: 1.6;
}
</style>
