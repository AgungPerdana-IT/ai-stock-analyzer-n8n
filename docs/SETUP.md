# ⚙️ Panduan Setup — AI Stock Analyzer

Panduan lengkap untuk menjalankan project ini dari nol.

---

## 📋 Prasyarat

Sebelum mulai, pastikan kamu sudah punya:

| Kebutuhan | Link | Keterangan |
|-----------|------|------------|
| **n8n** | [n8n.io](https://n8n.io) | Versi cloud (gratis) atau self-hosted |
| **Google AI Studio** | [aistudio.google.com](https://aistudio.google.com) | Untuk API Key Gemini |
| **Telegram Bot** | [@BotFather](https://t.me/BotFather) | Opsional, untuk pengiriman laporan |
| **NewsAPI** | [newsapi.org](https://newsapi.org) | Opsional, sumber berita tambahan |

---

## 🔑 Step 1 — Dapatkan API Keys

### Google Gemini API Key

1. Buka [Google AI Studio](https://aistudio.google.com)
2. Klik **"Get API Key"** di sidebar kiri
3. Klik **"Create API Key"**
4. Pilih project Google Cloud (atau buat baru)
5. Copy API Key — simpan di tempat aman

> ⚠️ Jangan pernah commit API Key ke GitHub! Gunakan Environment Variables.

### Telegram Bot (Opsional)

1. Buka Telegram, cari `@BotFather`
2. Kirim `/newbot`
3. Ikuti instruksi, beri nama bot kamu
4. Copy **Bot Token** yang diberikan
5. Dapatkan **Chat ID** kamu: buka `@userinfobot`, kirim sembarang pesan

---

## 📥 Step 2 — Import Workflow ke n8n

### Via n8n Cloud

1. Login ke [app.n8n.cloud](https://app.n8n.cloud)
2. Klik tombol **"+"** untuk buat workflow baru
3. Klik **"..."** (menu) → **"Import from file"**
4. Upload file `workflows/main_workflow.json`
5. Workflow akan muncul dengan semua node-nya

### Via n8n Self-Hosted

```bash
# Jika belum install n8n
npm install n8n -g

# Jalankan n8n
n8n start

# Buka browser: http://localhost:5678
# Import workflow sama seperti langkah di atas
```

---

## 🔐 Step 3 — Setup Credentials di n8n

### Google Gemini Credential

1. Di n8n, klik **"Credentials"** di menu kiri
2. Klik **"Add Credential"**
3. Cari **"Google Gemini"** atau **"HTTP Request"**
4. Masukkan API Key yang sudah kamu dapat
5. Save

### Telegram Credential (Opsional)

1. Di n8n, tambahkan credential baru
2. Pilih tipe **"Telegram"**
3. Masukkan **Bot Token**
4. Save

---

## 🎯 Step 4 — Konfigurasi Workflow

Setelah import, ada beberapa node yang perlu dikonfigurasi:

### Node: "Config" (Set Node pertama)

Ubah nilai berikut sesuai kebutuhanmu:

```javascript
// Daftar saham yang ingin dianalisis (kode BEI)
TARGET_STOCKS: "BBCA,TLKM,GOTO,BMRI,ASII"

// Bahasa berita
NEWS_LANGUAGE: "id"   // "id" untuk Indonesia, "en" untuk Inggris

// Jumlah berita per saham yang dianalisis
NEWS_PER_STOCK: 5
```

### Node: "Cron Trigger"

Default: setiap hari **Senin–Jumat jam 07.30 WIB**

Untuk mengubah jadwal:
- Klik node Cron
- Ubah nilai sesuai kebutuhan (format cron: `30 0 * * 1-5` = jam 07.30 UTC = 14.30 WIB)

> ⚠️ n8n menggunakan UTC. WIB = UTC+7, jadi kurangi 7 jam dari waktu yang kamu inginkan.

---

## ▶️ Step 5 — Test Run

Sebelum aktifkan schedule, test dulu secara manual:

1. Buka workflow di n8n
2. Klik **"Execute Workflow"** (tombol play di kanan atas)
3. Pantau setiap node — semua harus berstatus hijau ✅
4. Cek output di node terakhir — laporan harus muncul

Jika ada error, lihat bagian [Troubleshooting](#-troubleshooting) di bawah.

---

## ✅ Step 6 — Aktifkan Automation

Kalau test berhasil:

1. Toggle **"Active"** di kanan atas workflow menjadi ON
2. Workflow sekarang akan berjalan otomatis sesuai jadwal
3. Kamu akan menerima laporan di Telegram/Email setiap pagi

---

## 🔧 Troubleshooting

### Error: "Authentication failed" pada Gemini node
- Pastikan API Key benar dan tidak ada spasi tambahan
- Cek apakah Gemini API sudah di-enable di Google Cloud Console
- Pastikan billing aktif (Gemini gratis sampai batas tertentu)

### Error: "No news found"
- Cek koneksi internet n8n
- Pastikan keyword pencarian berita relevan
- Coba ganti sumber berita ke RSS feed lain

### Output JSON tidak bisa di-parse
- Buka `prompts/system_prompt.md`, pastikan instruksi format JSON tidak berubah
- Tambahkan error handling di node setelah Gemini
- Cek log Gemini — kadang model menambahkan teks di luar JSON

### Laporan Telegram tidak terkirim
- Pastikan bot sudah di-start (kirim `/start` ke bot kamu)
- Verifikasi Chat ID benar
- Cek apakah bot sudah join ke group (jika target group)

---

## 📞 Butuh Bantuan?

Buka [Issue](https://github.com/USERNAME/ai-stock-analyzer-n8n/issues) di GitHub dengan menyertakan:
- Screenshot error
- Versi n8n yang digunakan
- Node mana yang bermasalah
