<template>
  <HeaderComponent></HeaderComponent>
  <div class="container">
    <BalanceComponent :total="+total"></BalanceComponent>
    <IncomeExpensesComponent
      :income="+income"
      :expenses="+expenses"
    ></IncomeExpensesComponent>
    <TransactionListComponent
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransactionComponent
      @transactionSubmitted="handleTransactionSubmitted"
    ></AddTransactionComponent>
  </div>
</template>

<script setup>
import HeaderComponent from "./components/HeaderComponent.vue";
import BalanceComponent from "./components/BalanceComponent.vue";
import IncomeExpensesComponent from "./components/IncomeExpensesComponent.vue";
import TransactionListComponent from "./components/TransactionListComponent.vue";
import AddTransactionComponent from "./components/AddTransactionComponent.vue";

import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// [
//   { id: 1, text: "Flower", amount: -19.99 },
//   { id: 2, text: "Salary", amount: 299.99 },
//   { id: 3, text: "Book", amount: -12 },
//   { id: 4, text: "Camera", amount: 150 },
// ]

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

// Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueID(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  saveTransactionToLocalStorage();
  console.log(generateUniqueID());
  toast.success("Transaction added");
};

// Generate Unique Id
const generateUniqueID = () => {
  return Math.floor(Math.random() * Date.now());
};

// Delete transaction
const handleTransactionDeleted = (id) => {
  console.log(id);
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id != id
  );
  saveTransactionToLocalStorage();
  toast.success("Trnsaction deleted");
};

// Save to lacalstorage
const saveTransactionToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>

<style></style>
