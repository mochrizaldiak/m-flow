<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in', middleware: 'auth' })

const router = useRouter()
const nama = ref('')
const skor = ref(0)
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
  try {
    const token = localStorage.getItem('token')
    const res = await $fetch('http://localhost:8080/users/me', {
      headers: {
        Authorization: `Bearer ${token}`
      }
    })

    nama.value = res.nama || 'Pengguna M-Flow'
    skor.value = res.skor_keuangan || 0
    status.value = getStatus(skor.value)
    rekomendasi.value = getRekomendasi(skor.value)
  } catch (err) {
    console.error('Gagal mengambil data pengguna:', err)
    alert('Gagal mengambil data profil. Silakan coba lagi.')
  }
})

const logout = () => {
  if (confirm('Apakah kamu yakin ingin keluar?')) {
    localStorage.removeItem('token')
    alert('👋 Kamu telah keluar.')
    router.push('/login')
  }
}
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

    <button class="logout-btn" @click="logout">Keluar</button>
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

.logout-btn {
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 8px;
  padding: 10px 16px;
  font-size: 14px;
  font-weight: bold;
  cursor: pointer;
  width: 100%;
  margin-top: 12px;
  transition: background 0.2s;
}

.logout-btn:hover {
  background-color: #d32f2f;
}
</style>
