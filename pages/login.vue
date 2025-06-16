<template>
  <div class="auth-wrapper">
    <div class="auth-card">
      <h1 class="title">Selamat Datang 👋</h1>
      <p class="subtitle">
        Data kamu akan disimpan secara lokal di perangkat ini.<br />
        Aman dan tidak terkirim ke server mana pun.
      </p>

      <button class="login-btn" @click="showModal = true">
        Masuk ke M-Flow
      </button>
    </div>

    <BaseModal v-model="showModal">
      <template #header>
        <h3 class="modal-title">Privasi & Data</h3>
      </template>

      <template #default>
        <p>
          Data kamu akan disimpan <strong>lokal</strong> di browser ini menggunakan IndexedDB.
          <br />Tidak akan dikirim ke server mana pun.
        </p>
      </template>

      <template #footer>
        <button class="login-btn" @click="confirmLogin">Saya Mengerti</button>
      </template>
    </BaseModal>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { openDB } from 'idb'
import BaseModal from '~/components/BaseModal.vue'

const showModal = ref(false)
const router = useRouter()

const confirmLogin = async () => {
  const db = await openDB('mflow-db', 1, {
    upgrade(db) {
      if (!db.objectStoreNames.contains('auth')) {
        db.createObjectStore('auth', { keyPath: 'id' })
      }
    }
  })

  await db.put('auth', {
    id: 'session',
    isLoggedIn: true,
    createdAt: new Date().toISOString()
  })

  showModal.value = false
  router.push('/')
}
</script>

<style scoped>
.auth-wrapper {
  min-height: 100vh;
  background: linear-gradient(to bottom right, #6c92f4, #b4c5f8);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 16px;
}

.auth-card {
  width: 100%;
  max-width: 340px;
  background: var(--color-white, #ffffff);
  border-radius: 16px;
  padding: 28px 24px;
  text-align: center;
  box-shadow: 0 12px 28px rgba(0, 0, 0, 0.06);
  border: 1px solid rgba(0, 0, 0, 0.05);
}

.title {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 12px;
  color: #2e2e2e;
}

.subtitle {
  font-size: 14px;
  color: #5d5d5d;
  margin-bottom: 24px;
  line-height: 1.6;
}

.modal-title {
  font-size: 16px;
  font-weight: bold;
  color: var(--color-text, #2e2e2e);
  margin-bottom: 6px;
}

.login-btn {
  width: 100%;
  padding: 12px;
  font-size: 15px;
  font-weight: 600;
  background: #4a7efc;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  box-shadow: 0 2px 8px rgba(74, 126, 252, 0.25);
  transition: all 0.2s ease;
}

.login-btn:hover {
  background-color: #3c6fed;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(74, 126, 252, 0.3);
}
</style>
