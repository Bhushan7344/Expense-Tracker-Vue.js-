<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <div class="charts" v-if="transactions.length">
      <BarChart :transactions="transactions" />
      <PieChart :transactions="transactions" />
    </div>
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSubmittted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

import { ref, computed, onMounted } from 'vue'
import { useToast } from 'vue-toastification'
import BarChart from './components/BarChart.vue'
import PieChart from './components/PieChart.vue'

const toast = useToast()

const transactions = ref([])

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
  if (savedTransactions) {
    transactions.value = savedTransactions
  }
})

//Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
})

//Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2)
})

//Get Expense
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2)
})

//Add transaction
const handleTransactionSubmittted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  })
  saveTransactions()
  toast.success('Transaction added successfully')
}

//Generate Unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000000)
}

//Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  )
  saveTransactions()
  toast.success('Transaction deleted successfully')
}

const saveTransactions = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}
</script>

<style scoped>
.charts {
  display: flex;
  justify-content: space-between;
}
</style>
