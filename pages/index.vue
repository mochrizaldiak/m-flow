<script setup>
import BarChart from '~/components/BarChart.vue'

definePageMeta({
  layout: 'logged-in'
})

const balance = 6000
const warning = true
</script>

<template>
  <div class="page-wrapper">
    <div class="card">
      <div class="label">Balance</div>
      <div class="value">IDR {{ balance.toLocaleString('id-ID') }}</div>
    </div>

    <div class="card">
      <div class="label">Income vs Expenses</div>
      <BarChart />
    </div>

    <div v-if="warning" class="warning-box">
      ⚠️ Pengeluaran kamu hampir melebihi anggaran!
    </div>

    <div class="card">
      <div class="label">Rekomendasi Artikel</div>
      <div class="title">Cara Membuat Anggaran Sederhana</div>
      <p class="content">Pelajari dasar membuat anggaran pribadi hanya dalam 5 menit.</p>
      <div class="meta">Finance • 16 Juni 2025</div>
    </div>
  </div>
</template>

<style scoped>
.page-wrapper {
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* Text Styles */
.label {
  font-size: 14px;
  color: var(--color-text-light);
  margin-bottom: 6px;
}

.value {
  font-size: 20px;
  font-weight: bold;
  color: var(--color-text);
}

.title {
  font-size: 16px;
  font-weight: 600;
  color: var(--color-text);
}

.content {
  font-size: 14px;
  color: var(--color-text-light);
  margin-top: 6px;
}

.meta {
  font-size: 12px;
  color: gray;
  margin-top: 10px;
}
</style>
