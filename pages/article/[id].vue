<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

definePageMeta({ layout: 'logged-in' })

const route = useRoute()
const router = useRouter()
const article = ref(null)

const dummyArticles = [
  {
    id: 1,
    title: 'Cara Mengatur Uang dengan Mudah',
    content: `
      Mengelola keuangan pribadi adalah langkah pertama untuk mencapai kebebasan finansial.
      Mulailah dengan mencatat semua pemasukan dan pengeluaran harian kamu.
      Buat anggaran bulanan yang realistis dan konsisten meninjaunya setiap minggu.

      Jangan lupa sisihkan minimal 10% dari pendapatan untuk tabungan darurat.
      Gunakan aplikasi pencatat keuangan seperti M-Flow untuk memantau anggaran harian dan menghindari pengeluaran impulsif.

      Semakin kamu terbiasa mengelola uang dengan disiplin, semakin mudah kamu mencapai tujuan keuangan jangka panjang.
    `,
    category: 'finance',
    date: '2025-06-16'
  },
  {
    id: 2,
    title: 'Tips Menabung Buat Mahasiswa',
    content: `
      Menjadi mahasiswa bukan berarti tidak bisa menabung.
      Mulailah dari menyisihkan uang jajan mingguan, meskipun hanya Rp5.000.
      Gunakan rekening khusus tabungan agar tidak tergoda untuk membelanjakannya.

      Bawa bekal, manfaatkan promo, dan batasi nongkrong berlebihan. 
      Uang kecil yang kamu sisihkan hari ini bisa menjadi dana liburan atau darurat di masa depan.

      Dengan konsistensi dan niat, kamu bisa punya tabungan meski penghasilan terbatas.
    `,
    category: 'saving',
    date: '2025-06-10'
  }
]

onMounted(() => {
  const id = Number(route.params.id)
  const found = dummyArticles.find(a => a.id === id)

  if (!found) {
    alert('Artikel tidak ditemukan.')
    router.push('/artikel')
  } else {
    article.value = found
  }
})

console.log("hello")
</script>

<template>
  <div v-if="article" class="article-wrapper">
    <h1 class="article-title">{{ article.title }}</h1>
    <div class="article-meta">
      <span class="category">{{ article.category }}</span>
      <span>•</span>
      <span class="date">{{ article.date }}</span>
    </div>
    <div class="article-content" v-html="formatParagraphs(article.content)"></div>
  </div>
</template>

<script>
function formatParagraphs(content) {
  return content
    .trim()
    .split('\n')
    .map(p => `<p>${p.trim()}</p>`)
    .join('')
}
</script>

<style scoped>
.article-wrapper {
  padding: 24px 16px;
  max-width: 375px;
  margin: 0 auto;
  background-color: var(--color-white);
}

.article-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 8px;
  color: var(--color-text);
  line-height: 1.3;
}

.article-meta {
  font-size: 13px;
  color: var(--color-text-light);
  margin-bottom: 16px;
  display: flex;
  gap: 6px;
  align-items: center;
}

.article-content p {
  font-size: 14px;
  color: var(--color-text);
  line-height: 1.7;
  margin-bottom: 14px;
  text-align: justify;
}
</style>
