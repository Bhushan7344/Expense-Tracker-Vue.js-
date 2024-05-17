<template>
  <div>
    <Pie :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup>
import { defineProps, computed } from 'vue'
import { Pie } from 'vue-chartjs'
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  ArcElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js'

ChartJS.register(CategoryScale, LinearScale, ArcElement, Title, Tooltip, Legend)

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
})

const chartData = computed(() => {
  const labels = props.transactions.map((transaction) => transaction.text)
  const data = props.transactions.map((transaction) =>
    Math.abs(transaction.amount)
  ) // Absolute values for pie chart
  const backgroundColors = props.transactions.map(
    (transaction) => (transaction.amount > 0 ? '#2196f3' : '#f44336') // Blue for income, red for expenses
  )

  return {
    labels: ['Income', 'Expense'],
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
      display: true,
      labels: {
        usePointStyle: true,
        generateLabels: function (chart) {
          const { data } = chart
          if (data.labels.length && data.datasets.length) {
            return data.labels.map((label, index) => {
              const dataset = data.datasets[0]
              const color = dataset.backgroundColor[index]
              const labelName =
                dataset.backgroundColor[index] === '#2196f3'
                  ? 'Income'
                  : 'Expense'
              return {
                text: labelName,
                fillStyle: color,
                hidden: isNaN(dataset.data[index]) || dataset.data[index] === 0,
                index: index,
              }
            })
          }
          return []
        },
      },
    },
    title: {
      display: true,
      text: 'Transactions Overview',
    },
  },
}
</script>

<style scoped>
/* Add any necessary styles here */
</style>
