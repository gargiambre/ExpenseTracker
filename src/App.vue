<script setup>

import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import Transaction from './components/Transaction.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from "vue-toastification";

import { ref, computed, onMounted } from 'vue';

const toast = useToast();

const transactions = ref([]);


onMounted(() =>
{
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if(savedTransactions)
  {
    transactions.value = savedTransactions;
  }
});

const total = computed (() => 
{
  return transactions.value.reduce((acc, transaction) => 
  {
    return acc + transaction.amount;    
  }, 0);
});


const income = computed (() => 
{
  return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction) => 
  {
    return acc + transaction.amount;    
  }, 0)
  .toFixed(2);   // two decimal places
});


const expenses = computed (() => 
{
  return transactions.value.filter((transaction) => transaction.amount < 0).reduce((acc, transaction) => 
  {
    return acc + transaction.amount;    
  }, 0);
});


const handleTransactionSubmitted = (transactionData) =>
{
  // console.log(transactionData);
  transactions.value.push(
  {
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  savedTransactionsToLocalStorage();

  // console.log(generateUniqueId());
  toast.success('Transaction added')
};

const generateUniqueId = () =>
{
  return Math.floor(Math.random() * 1000000);
};

const handleTransactionDeleted = (id) =>
{
  // console.log(id);
  transactions.value = transactions.value.filter((transaction) =>transaction.id !== id)

  savedTransactionsToLocalStorage();

  toast.success('Transaction Deleted');
};

const savedTransactionsToLocalStorage = () =>
{
  localStorage.setItem('transaction', JSON.stringify(transactions.value))
};


</script>



<template>
  <Header />
    <div class="container">
      <Balance :total="total"/>
      <IncomeExpense :income="+income" :expenses="+expenses" />
      <Transaction :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
      <AddTransaction  @transactionSubmitted="handleTransactionSubmitted"/>
    </div>
  
</template>



<style>
@import url('https://fonts.googleapis.com/css?family=Lato&display=swap');

:root {
  --box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

* {
  box-sizing: border-box;
}

body {
  background-color: #240606;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  margin: 0;
  font-family: 'Lato', sans-serif;
  font-size: 18px;
} 

.container {
  margin: 30px auto;
  width: 400px;
}

h1 {
  letter-spacing: 1px;
  margin: 0;
}

h3 {
  border-bottom: 1px solid #460707;
  padding-bottom: 10px;
  margin: 40px 0 10px;
}

h4 {
  margin: 0;
  text-transform: uppercase;
  color: #460707;
}

.inc-exp-container {
  background-color: #fcf6f6;
  box-shadow: var(--box-shadow);
  padding: 20px;
  display: flex;
  justify-content: space-between;
  margin: 20px 0;
}

.inc-exp-container > div {
  flex: 1;
  text-align: center;
}

.inc-exp-container > div:first-of-type {
  border-right: 1px solid #949494;
}

.money {
  font-size: 20px;
  letter-spacing: 1px;
  margin: 5px 0;
}

.money.plus {
  color: #2ecc71;
}

.money.minus {
  color: #c0392b;
}

label {
  display: inline-block;
  margin: 10px 0;
}

input[type='text'],
input[type='number'] {
  border: 1px solid #570404;
  border-radius: 2px;
  display: block;
  font-size: 16px;
  padding: 10px;
  width: 100%;
}

.btn {
  cursor: pointer;
  background-color: #9c88ff;
  box-shadow: var(--box-shadow);
  color: #880000;
  border: 0;
  display: block;
  font-size: 16px;
  margin: 10px 0 30px;
  padding: 10px;
  width: 100%;
}

.btn:focus,
.delete-btn:focus {
  outline: 0;
}

.list {
  list-style-type: none;
  padding: 0;
  margin-bottom: 40px;
}

.list li {
  background-color: #fff;
  box-shadow: var(--box-shadow);
  color: #333;
  display: flex;
  justify-content: space-between;
  position: relative;
  padding: 10px;
  margin: 10px 0;
}

.list li.plus {
  border-right: 5px solid #2ecc71;
}

.list li.minus {
  border-right: 5px solid #c0392b;
}

.delete-btn {
  cursor: pointer;
  background-color: #e74c3c;
  border: 0;
  color: #fff;
  font-size: 20px;
  line-height: 20px;
  padding: 2px 5px;
  position: absolute;
  top: 50%;
  left: 0;
  transform: translate(-100%, -50%);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.list li:hover .delete-btn {
  opacity: 1;
}  
</style> 
