<template>
  <div>
    <Bar :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup>
import { defineProps, computed } from 'vue'
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  BarElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js'

ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend)

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
})

const chartData = computed(() => {
  const labels = props.transactions.map((transaction) => transaction.text)
  const data = props.transactions.map((transaction) => transaction.amount)
  const backgroundColors = props.transactions.map((transaction) =>
    transaction.amount > 0 ? '#4caf50' : '#f44336'
  )

  return {
    labels,
    datasets: [
      {
        label: 'Amount (INR)',
        data,
        backgroundColor: backgroundColors,
      },
    ],
  }
})

const chartOptions = {
  responsive: true,
  plugins: {
    legend: {
      display: false,
    },
    title: {
      display: true,
      text: 'Transactions Overview',
    },
  },
}
</script>
