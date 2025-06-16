<script setup>
import { defineProps } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const props = defineProps({
  id: [Number, String],
  name: String,
  type: String, // 'primary' | 'non-primary'
  income: Number,
  expense: Number,
  start: String,
  end: String
})

const goToDetail = () => {
  router.push(`/budget/${props.id}`)
}
</script>

<template>
  <div class="budget-card" @click="goToDetail">
    <div class="card-header">
      <h3 class="budget-name">{{ name }}</h3>
      <span class="badge" :class="type">{{ type === 'primary' ? 'Primer' : 'Non-primer' }}</span>
    </div>
    <div class="budget-info">
      <p>💰 Income: Rp {{ income.toLocaleString('id-ID') }}</p>
      <p>💸 Expense: Rp {{ expense.toLocaleString('id-ID') }}</p>
      <p>📅 {{ start }} → {{ end }}</p>
      <p :class="{ over: expense > income }">
        {{ expense > income ? '⚠️ Overbudget' : 'Sisa: Rp ' + (income - expense).toLocaleString('id-ID') }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.budget-card {
  background: #fff;
  padding: 16px;
  border-radius: 12px;
  border: 1px solid #eee;
  box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  cursor: pointer;
  transition: background 0.2s;
}

.budget-card:hover {
  background-color: #f9f9f9;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.budget-name {
  font-size: 16px;
  font-weight: bold;
  color: var(--color-text);
}

.badge {
  font-size: 12px;
  padding: 2px 8px;
  border-radius: 999px;
  font-weight: bold;
  text-transform: capitalize;
}

.badge.primary {
  background-color: #e6f0ff;
  color: #2c7be5;
}

.badge.non-primary {
  background-color: #fce4ec;
  color: #d81b60;
}

.budget-info {
  font-size: 14px;
  color: #555;
  line-height: 1.6;
}

.over {
  color: #c62828;
  font-weight: bold;
}
</style>
