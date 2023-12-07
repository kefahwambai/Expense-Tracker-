<template>
  <Header/>
  <div class="container">
    <Balance :total="total"/>
    <IncomeExpenses :income="+income" :expenses="-expenses"/>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
    <Addtransaction @transactionSubmitted="handleTransactionSubmitted"/>


  </div>
</template>

<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue'
  import IncomeExpenses from './components/IncomeExpenses.vue'
  import TransactionList from './components/TransactionList.vue'
  import Addtransaction from './components/Addtransaction.vue'
  import { useToast } from "vue-toastification"
  import { ref, computed, onMounted } from 'vue';

  const toast = useToast()
  const  transactions = ref([
  ]);        
  
  onMounted(() => {
    const SavedTransactions = JSON.parse(localStorage.getItem
    ('transactions'));

    if (SavedTransactions) {
      transactions.value = SavedTransactions;
    }
  });

  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount      
    }, 0)
  });


  const income = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount      
    }, 0).toFixed(2);
  });

  const expenses = computed(() => {
    return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount      
    }, 0).toFixed(2);
  });

  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount
    }) 

    saveTransactionsToLocalStorage();
    toast.success('Transaction added;')

  }

  const generateUniqueId = () => {
    return Math.floor(Math.random() * 100000)
  }

  const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id);
    saveTransactionsToLocalStorage();
    toast.success('Transaction deleted')
  };

  const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value))
  }

  

</script>