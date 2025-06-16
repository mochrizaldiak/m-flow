<script setup>
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'
import { Bar } from 'vue-chartjs'
import { computed } from 'vue'

// Registrasi chart.js
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

// Props dari parent
const props = defineProps({
  month: Number,
  year: Number
})

// Data dummy tergantung bulan (pakai computed)
const chartData = computed(() => {
  const monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
  const selectedMonth = props.month
  const selectedYear = props.year

  // Buat dummy 4 minggu dalam 1 bulan
  return {
    labels: ['Minggu 1', 'Minggu 2', 'Minggu 3', 'Minggu 4'],
    datasets: [
      {
        label: 'Pemasukan',
        data: generateRandomData(4, 500, 1000),
        backgroundColor: '#60a5fa'
      },
      {
        label: 'Pengeluaran',
        data: generateRandomData(4, 400, 900),
        backgroundColor: '#f87171'
      }
    ]
  }
})

// Fungsi bantu buat generate angka acak
function generateRandomData(length, min, max) {
  return Array.from({ length }, () => Math.floor(Math.random() * (max - min + 1)) + min)
}

// Opsi chart
const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: { position: 'bottom' },
    title: { display: false }
  },
  scales: {
    y: {
      beginAtZero: true,
      ticks: { stepSize: 200 }
    }
  }
}
</script>

<template>
  <div style="height: 200px;">
    <Bar :data="chartData" :options="chartOptions" />
  </div>
</template>
