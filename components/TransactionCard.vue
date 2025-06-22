<script setup>
import { defineProps } from 'vue'

const props = defineProps({
  id: [String, Number],
  type: String, 
  amount: Number,
  category: String,
  date: String,
  budgetName: String
})
</script>

<template>
  <NuxtLink :to="`/transaction/${id}`" class="transaksi-card-link">
    <div class="transaksi-card">
      <div class="top">
        <span class="amount" :class="type">
          {{ type === 'income' ? '+' : '-' }}Rp {{ amount.toLocaleString('id-ID') }}
        </span>
        <span class="category-chip" :class="type">
          {{ type === 'income' ? 'Pemasukan' : 'Pengeluaran' }}
        </span>
      </div>
      <div class="meta">
        {{ category }} • {{ date }}
      </div>
      <div class="budget-info">
        Termasuk dalam anggaran: <strong>{{ budgetName }}</strong>
      </div>
    </div>
  </NuxtLink>
</template>

<style scoped>
.transaksi-card-link {
  text-decoration: none;
  color: inherit;
}

.transaksi-card {
  background: #fff;
  border: 1px solid #eee;
  border-radius: 12px;
  padding: 12px 16px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  transition: background 0.1s ease;
}

.transaksi-card:hover {
  background-color: #f9f9f9;
}

.top {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.amount {
  font-size: 16px;
  font-weight: bold;
}

.amount.income {
  color: #2e7d32;
}

.amount.expense {
  color: #c62828;
}

.category-chip {
  font-size: 12px;
  padding: 2px 10px;
  border-radius: 999px;
  font-weight: 600;
}

.category-chip.income {
  background: #e6f4ea;
  color: #2e7d32;
}

.category-chip.expense {
  background: #fdecea;
  color: #c62828;
}

.meta {
  font-size: 13px;
  color: #666;
  margin-top: 6px;
}

.budget-info {
  font-size: 13px;
  color: #444;
  margin-top: 4px;
}
</style>
