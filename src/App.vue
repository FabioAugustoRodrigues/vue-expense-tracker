<template>
  <Header></Header>

  <div class="container">
    <Balance :total="+total"></Balance>
    <IncomeExpenses :income="+income" :expenses="+expenses"></IncomeExpenses>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"></TransactionList>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"></AddTransaction>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';

import { ref, computed } from 'vue';

const transactions = ref([
  { id: 1, text: 'Flower', amount: -20 },
  { id: 2, text: 'Salary', amount: 100 }
]);

const toast = useToast();

// get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  toast.success('Transaction added');
};

// generate unique id
const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000);
}

// delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

  toast.success('Transaction deleted');
}

</script>