<script setup>
import { ref, computed, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";

definePageMeta({ layout: "logged-in", middleware: "auth" });

const route = useRoute();
const router = useRouter();

const budget = ref(null);
const transactions = ref([]);
const loading = ref(true);

const relatedTransactions = computed(() =>
  transactions.value.filter((t) => t.budget_id === budget.value?.id)
);

const incomeTotal = computed(() =>
  relatedTransactions.value
    .filter((t) => t.jenis === "pemasukan")
    .reduce((sum, t) => sum + t.nominal, 0)
);

const expenseTotal = computed(() =>
  relatedTransactions.value
    .filter((t) => t.jenis === "pengeluaran")
    .reduce((sum, t) => sum + t.nominal, 0)
);

const status = computed(() => {
  if (!budget.value) return "";
  if (budget.value.status === "S") return "Selesai";
  return "Aktif";
});

const loadData = async () => {
  try {
    loading.value = true;
    const token = localStorage.getItem("token");
    const id = route.params.id;

    const budgetRes = await $fetch(`http://localhost:8080/budgets/${id}`, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });

    const trxRes = await $fetch(
      `http://localhost:8080/transactions/?budget_id=${id}`,
      {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      }
    );

    budget.value = budgetRes;
    transactions.value = trxRes;
  } catch (err) {
    console.error("❌ Gagal mengambil data anggaran:", err);
    alert("Anggaran tidak ditemukan.");
    router.push("/budget");
  } finally {
    loading.value = false;
  }
};

const handleEdit = () => {
  router.push(`/budget/edit/${budget.value.id}`);
};

const formatEndDate = (startDateStr, periode) => {
  const startDate = new Date(startDateStr)
  const endDate = new Date(startDate)

  switch (periode) {
    case 'D':
      endDate.setDate(endDate.getDate() + 1)
      break
    case 'W':
      endDate.setDate(endDate.getDate() + 7)
      break
    case 'M':
      endDate.setDate(endDate.getDate() + 30)
      break
    case 'Y':
      endDate.setFullYear(endDate.getFullYear() + 1)
      break
  }

  return endDate.toLocaleDateString('id-ID')
}

const handleDelete = async () => {
  const confirmDelete = confirm(
    "Apakah kamu yakin ingin menghapus anggaran ini?"
  );
  if (!confirmDelete) return;

  try {
    const token = localStorage.getItem("token");
    await $fetch(`http://localhost:8080/budgets/${budget.value.id}`, {
      method: "DELETE",
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });

    alert("✅ Anggaran berhasil dihapus.");
    router.push("/budget");
  } catch (err) {
    console.error("❌ Gagal menghapus anggaran:", err);
    alert("Gagal menghapus anggaran.");
  }
};

onMounted(loadData);
</script>

<template>
  <div v-if="budget" class="page-wrapper">
    <h2 class="page-title">{{ budget.nama }}</h2>

    <div class="status-badge" :class="status.toLowerCase()">{{ status }}</div>

    <div class="detail-group">
      <label>Jenis</label>
      <p>{{ budget.jenis_anggaran === "primary" ? "Primer" : "Non-primer" }}</p>
    </div>

    <div class="detail-group">
      <label>Periode</label>
      <p>
        {{ new Date(budget.tanggal).toLocaleDateString("id-ID") }} →
        {{ formatEndDate(budget.tanggal, budget.jenis_periode) }}
      </p>
    </div>

    <div class="detail-group">
      <label>Total Pemasukan</label>
      <p class="text-green">Rp {{ incomeTotal.toLocaleString("id-ID") }}</p>
    </div>

    <div class="detail-group">
      <label>Total Pengeluaran</label>
      <p class="text-red">Rp {{ expenseTotal.toLocaleString("id-ID") }}</p>
    </div>

    <div class="detail-group">
      <label>Selisih</label>
      <p :class="incomeTotal - expenseTotal >= 0 ? 'text-green' : 'text-red'">
        Rp {{ (incomeTotal - expenseTotal).toLocaleString("id-ID") }}
      </p>
    </div>

    <div class="button-group">
      <button class="btn edit" :disabled="budget.status === 'S'" @click="handleEdit">✏️ Edit</button>
      <button class="btn delete" @click="handleDelete">🗑️ Hapus</button>
    </div>

    <hr class="divider" />

    <div class="transactions-section">
      <h3 class="section-title">Transaksi Terkait</h3>
      <div v-if="relatedTransactions.length === 0" class="empty-state">
        Belum ada transaksi untuk anggaran ini.
      </div>
      <div v-else class="transaction-list">
        <div v-for="t in relatedTransactions" :key="t.id" class="trans-item">
          <div class="top">
            <span :class="t.jenis === 'pemasukan' ? 'text-green' : 'text-red'">
              {{ t.jenis === "pemasukan" ? "+" : "-" }}Rp
              {{ t.nominal.toLocaleString("id-ID") }}
            </span>
            <span class="category">{{ t.kategori }}</span>
          </div>
          <div class="date">
            {{ new Date(t.tanggal).toLocaleDateString("id-ID") }}
          </div>
        </div>
      </div>
    </div>
  </div>

  <div v-else-if="loading" class="loading">Memuat data...</div>
</template>

<style scoped>
  .page-wrapper {
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 14px;
  }
  .page-title {
    font-size: 20px;
    font-weight: bold;
  }
  .status-badge {
    padding: 4px 10px;
    font-size: 12px;
    font-weight: bold;
    border-radius: 999px;
    width: fit-content;
  }
  .status-badge.aktif {
    background-color: #e0f3ff;
    color: #2271b1;
  }
  .status-badge.selesai {
    background-color: #e9fbe5;
    color: #2e7d32;
  }
  .status-badge.overbudget {
    background-color: #ffe4e4;
    color: #c62828;
  }
  .detail-group label {
    font-size: 13px;
    font-weight: bold;
    color: #666;
  }
  .detail-group p {
    font-size: 14px;
    margin-top: 2px;
    margin-bottom: 10px;
  }
  .text-green {
    color: #2e7d32;
    font-weight: 500;
  }
  .text-red {
    color: #c62828;
    font-weight: 500;
  }
  .button-group {
    display: flex;
    gap: 12px;
    margin-top: 8px;
  }
  .btn {
    padding: 10px 16px;
    border: none;
    border-radius: 8px;
    font-weight: bold;
    font-size: 14px;
    cursor: pointer;
  }
  .btn.edit {
    background-color: #2c7be5;
    color: white;
  }
  .btn.edit:disabled {
    background-color: #ccc;
    color: #888;
    cursor: not-allowed;
  }
  .btn.delete {
    background-color: #f44336;
    color: white;
  }
  .section-title {
    font-size: 16px;
    font-weight: bold;
    margin-bottom: 6px;
  }
  .transaction-list {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  .trans-item {
    padding: 10px 12px;
    border: 1px solid #eee;
    border-radius: 8px;
    background: #fafafa;
  }
  .top {
    display: flex;
    justify-content: space-between;
    font-size: 14px;
    font-weight: 500;
  }
  .category {
    color: #555;
  }
  .date {
    font-size: 12px;
    color: #888;
    margin-top: 4px;
  }
  .empty-state {
    font-size: 14px;
    color: #888;
  }
  .divider {
    margin-top: 24px;
    margin-bottom: 8px;
    border: none;
    border-top: 1px solid #eee;
  }
  .loading {
    text-align: center;
    font-size: 14px;
    color: #888;
    margin-top: 32px;
  }
</style>
