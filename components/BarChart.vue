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

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const chartData = {
  labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May'],
  datasets: [
    {
      label: 'Income',
      data: [500, 600, 700, 650, 800],
      backgroundColor: '#60a5fa'
    },
    {
      label: 'Expense',
      data: [400, 500, 300, 600, 500],
      backgroundColor: '#f87171'
    }
  ]
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
