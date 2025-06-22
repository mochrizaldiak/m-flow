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

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const props = defineProps({
  month: Number,
  year: Number
})

const chartData = computed(() => {
  const monthNames = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
  const selectedMonth = props.month
  const selectedYear = props.year

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

function generateRandomData(length, min, max) {
  return Array.from({ length }, () => Math.floor(Math.random() * (max - min + 1)) + min)
}

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
