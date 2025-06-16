<template>
  <div class="auth-wrapper">
    <div class="auth-card">
      <h1 class="title">Daftar Akun</h1>
      <p class="subtitle">Silakan isi data untuk membuat akun baru.</p>

      <form @submit.prevent="handleRegister">
        <div class="form-group">
          <label>Email</label>
          <input
            v-model="email"
            type="email"
            class="input"
            placeholder="contoh@email.com"
            required
          />
        </div>

        <div class="form-group">
          <label>Password</label>
          <div class="password-wrapper">
            <input
              v-model="password"
              :type="showPassword ? 'text' : 'password'"
              class="input"
              placeholder="Buat password..."
              required
            />
            <button type="button" class="eye-btn" @click="showPassword = !showPassword">
              <LucideEye v-if="!showPassword" size="18" />
              <LucideEyeOff v-else size="18" />
            </button>
          </div>
        </div>

        <div class="form-group">
          <label>Konfirmasi Password</label>
          <div class="password-wrapper">
            <input
              v-model="confirmPassword"
              :type="showConfirm ? 'text' : 'password'"
              class="input"
              placeholder="Ulangi password..."
              required
            />
            <button type="button" class="eye-btn" @click="showConfirm = !showConfirm">
              <LucideEye v-if="!showConfirm" size="18" />
              <LucideEyeOff v-else size="18" />
            </button>
          </div>
        </div>

        <button type="submit" class="register-btn">Daftar</button>
      </form>

      <p class="login-text">
        Sudah punya akun?
        <span class="login-link" @click="router.push('/login')">Masuk di sini</span>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { Eye as LucideEye, EyeOff as LucideEyeOff } from 'lucide-vue-next'

const router = useRouter()
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const showPassword = ref(false)
const showConfirm = ref(false)

// Dummy user list simpan di memori (tanpa persist)
const dummyUsers = ref([])

const handleRegister = () => {
  if (!email.value || !password.value || !confirmPassword.value) {
    alert('Harap lengkapi semua field.')
    return
  }

  if (password.value !== confirmPassword.value) {
    alert('Konfirmasi password tidak cocok.')
    return
  }

  const alreadyExists = dummyUsers.value.find(u => u.email === email.value)
  if (alreadyExists) {
    alert('Email sudah terdaftar.')
    return
  }

  dummyUsers.value.push({ email: email.value, password: password.value })
  alert('✅ Pendaftaran berhasil! Silakan login.')
  router.push('/login')
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
  max-width: 360px;
  background: #ffffff;
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

.form-group {
  display: flex;
  flex-direction: column;
  text-align: left;
  margin-bottom: 16px;
}

.form-group label {
  font-size: 13px;
  color: #333;
  margin-bottom: 4px;
}

.input {
  padding: 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 8px;
  width: 100%;
}

.password-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.eye-btn {
  position: absolute;
  display: flex;
  right: 10px;
  background: none;
  border: none;
  cursor: pointer;
  color: #666;
  padding: 4px;
}

.register-btn {
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
  margin-top: 8px;
}

.register-btn:hover {
  background-color: #3c6fed;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(74, 126, 252, 0.3);
}

.login-text {
  font-size: 13px;
  color: #5d5d5d;
  margin-top: 16px;
}

.login-link {
  color: #2c7be5;
  font-weight: 600;
  cursor: pointer;
  margin-left: 4px;
}
</style>
