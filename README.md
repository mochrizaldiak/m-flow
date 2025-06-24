# M-Flow – Manajemen Keuangan Mahasiswa

Aplikasi berbasis web untuk membantu mahasiswa mengelola keuangan, membuat anggaran, mencatat transaksi, dan mendapatkan rekomendasi finansial secara personal.

## 🛠️ Fitur Utama

- 🔐 Autentikasi (login/register)
- 💸 Catatan transaksi pemasukan dan pengeluaran
- 🎯 Manajemen anggaran (primer & non-primer)
- 📊 Visualisasi saldo dan transaksi
- 📚 Artikel edukasi keuangan
- ✅ Dashboard skor keuangan dan rekomendasi

---

## 🚀 Menjalankan Proyek

### 1. **Clone Repository**

```bash
git clone https://github.com/username/mflow.git
cd mflow
```

### 2. **Install Dependencies**

```bash
npm install
```

### 3. **Jalankan Backend (Golang)**

Pastikan server Golang berjalan di `http://localhost:8080`.

Contoh:

```bash
cd backend
go run main.go
```

> ⚠️ Pastikan database sudah terhubung (misal: PostgreSQL/MySQL) dan migrasi dijalankan jika ada.

### 4. **Jalankan Frontend (Nuxt 3)**

```bash
npm run dev
```

Frontend akan berjalan di: `http://localhost:3000`

---

## ⚙️ Konfigurasi

### `.env` (opsional)

Jika kamu ingin menyimpan konfigurasi `baseURL`:

```env
NUXT_API_BASE=http://localhost:8080
```

> Gunakan `$fetch` seperti ini:
> ```js
> const res = await $fetch('/users/me', { baseURL: useRuntimeConfig().public.NUXT_API_BASE })
> ```

---

## 🔐 Autentikasi

- Token disimpan di `localStorage` (key: `token`)
- Halaman selain `/login` dan `/register` akan dicek menggunakan middleware `auth`
- Pengguna yang sudah login tidak bisa mengakses `/login` dan `/register`

---

## 📁 Struktur Direktori Utama

```
├─ pages/
│  ├─ index.vue              → Dashboard
│  ├─ login.vue              → Login user
│  ├─ register.vue           → Register user
│  ├─ transaction/           → Transaksi (add/edit/detail)
│  ├─ budget/                → Anggaran (add/edit/detail)
│  ├─ article/               → Artikel
│  ├─ profile.vue            → Profil pengguna
├─ components/
├─ middleware/
│  └─ auth.js                → Cek token untuk akses halaman
```

---

## 👥 Kontributor

- 🧑 Rizaldi (Frontend)
- 🧑‍💻 Rizaldi (Backend) [mflow-service](https://github.com/mochrizaldiak/mflow-service)

---

## ✅ Lisensi

MIT License. Silakan digunakan dan dikembangkan lebih lanjut sesuai kebutuhan.
