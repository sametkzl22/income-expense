<template>
    <Header />

    <div class="container">
      <Balance :total="+total" />
      <IncomeExpenses :income="+income" :expense="+expenses"/>
      <TransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted"/>
      <AddTransaction 
      @transaction-submitted="handleTransactionSubmitted"/>
    </div>


</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { useToast } from 'vue-toastification';

import { computed, ref, onMounted } from 'vue';

const toast = useToast();


const transactions =ref( []);
onMounted(() => {
    const storedTransactions = localStorage.getItem('transactions');
    if (storedTransactions) {
        transactions.value = JSON.parse(storedTransactions);
    }
});

//Get total    
const total = computed(() => {
    return transactions.value.reduce((acc, transaction) =>{
    return acc + transaction.amount;}, 0);
});    

// Get income 
const income = computed(() => {
    return transactions.value
    .filter(transaction => transaction.amount > 0)
    .reduce((acc, transaction) =>
    {return acc + transaction.amount;}, 0)
    .toFixed(2);

});


//Get expenses
const expenses = computed(() => {
    return transactions.value
    .filter(transaction => transaction.amount < 0)
    .reduce((acc, transaction) =>{return acc + transaction.amount;}, 0) * -1
    .toFixed(2);
});

//Add transaction 
const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount
    });
    storeTransactionsToLocalStorage();

    toast.success("Transaction added successfully");

    console.log(generateUniqueId()) ;
};
    //Generate unique ID

    const generateUniqueId = () => {
        return Math.floor(Math.random() * 100000000);
    };

    //Delete transaction
    const handleTransactionDeleted = (id) => {
        transactions.value = transactions.value.filter(transaction => transaction.id !== id);
        toast.success("Transaction deleted successfully");
        storeTransactionsToLocalStorage();
    };
    

    //Store to locastorage
    const storeTransactionsToLocalStorage = () => {
        localStorage.setItem('transactions', JSON.stringify(transactions.value));
    };

 
    

</script>