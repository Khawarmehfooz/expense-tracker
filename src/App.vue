<script setup>
    import Header from './components/Header.vue';
    import Balance from './components/Balance.vue'
    import IncomeExpenses from './components/IncomeExpenses.vue'
    import TransactionList from './components/TransactionList.vue'
    import AddTransaction from './components/AddTransaction.vue'
    import {ref, computed,onMounted} from 'vue';
    import { useToast } from 'vue-toastification'
    const toast = useToast()

    const transactions = ref([])
    onMounted(()=>{
        const savedTransaction = JSON.parse(localStorage.getItem('transactions'))
        if(savedTransaction){
            transactions.value = savedTransaction;
        }
    })

    const total = computed(()=>{
        return transactions.value.reduce((acc,transaction)=>{
            return acc + transaction.amount
        },0)
    });
    const income = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount
        }, 0).toFixed(2)
    })
    const expenses = computed(()=>{
        return transactions.value
            .filter((transaction)=>transaction.amount < 0)
            .reduce((acc,transaction)=>{
                return acc + transaction.amount
            },0).toFixed(2)
    })
    function generateUniqueId(){
        return Math.floor(Math.random()*10000000)
    }
    const handleTransactionSubmitted = (transactionData)=>{
        transactions.value.push({
            id: generateUniqueId(),
            text: transactionData.text,
            amount: transactionData.amount
        })
        saveToLocalStorage()
        toast.success('Transaction Added')
    }
    function handleTransactionDeleted(id){
        transactions.value = transactions.value.filter((transaction)=>transaction.id !== id
        );
        saveToLocalStorage();
        toast.success('Transaction Deleted')

    }
    function saveToLocalStorage(){
        localStorage.setItem('transactions',JSON.stringify(transactions.value))
    }
</script>
<template>
    <Header/>
    <Balance :total="+total"/>
    <IncomeExpenses 
        :income="+income" 
        :expenses="+expenses" />
    <TransactionList 
        :transactions="transactions" 
        @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
</template>