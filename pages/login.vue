<template>
  <div class="auth-wrapper">
    <div class="auth-card">
      <h1 class="title">Masuk ke M-Flow</h1>
      <p class="subtitle">Gunakan email dan password untuk login.</p>

      <form @submit.prevent="handleLogin">
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
              placeholder="••••••••"
              required
            />
            <button type="button" class="eye-btn" @click="togglePassword">
              <LucideEye v-if="!showPassword" size="18" />
              <LucideEyeOff v-else size="18" />
            </button>
          </div>
        </div>

        <button class="login-btn" type="submit">Login</button>
      </form>

      <p class="register-text">
        Belum punya akun?
        <span class="register-link" @click="router.push('/register')">Daftar di sini</span>
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { Eye as LucideEye, EyeOff as LucideEyeOff } from 'lucide-vue-next'

const email = ref('')
const password = ref('')
const showPassword = ref(false)
const router = useRouter()

const togglePassword = () => {
  showPassword.value = !showPassword.value
}

const handleLogin = async () => {
  try {
    const res = await $fetch('http://localhost:8080/users/login', {
      method: 'POST',
      body: {
        email: email.value,
        password: password.value
      }
    })

    alert('✅ Login berhasil!')

    localStorage.setItem('token', res.token)

    router.push('/')
  } catch (err) {
    const message = err?.data?.message || '❌ Terjadi kesalahan saat login.'
    alert(message)
  }
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
  width: 100%;
  padding: 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 8px;
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
  margin-top: 8px;
}

.login-btn:hover {
  background-color: #3c6fed;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(74, 126, 252, 0.3);
}

.register-text {
  font-size: 13px;
  color: #5d5d5d;
  margin-top: 16px;
}

.register-link {
  color: #2c7be5;
  font-weight: 600;
  cursor: pointer;
  margin-left: 4px;
}
</style>
