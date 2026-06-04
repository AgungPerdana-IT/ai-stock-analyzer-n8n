# ⚙️ Panduan Setup — AI Stock Analyzer

Panduan lengkap membangun workflow dari nol.

---

## Prasyarat

| Kebutuhan | Link | Keterangan |
|-----------|------|------------|
| **Node.js** | [nodejs.org](https://nodejs.org) | v18+ |
| **n8n** | Install via npm | Self-hosted, gratis |
| **NewsAPI Key** | [newsapi.org/register](https://newsapi.org/register) | Free tier: 100 req/hari |
| **Gemini API Key** | [aistudio.google.com](https://aistudio.google.com) | Free tier tersedia |
| **Telegram Bot** | [@BotFather](https://t.me/BotFather) | Untuk pengiriman laporan |

---

## Step 1 — Install n8n

```bash
# Install (pakai sudo jika permission error)
sudo npm install n8n -g

# Jalankan
n8n start

# Buka browser
# http://localhost:5678
```

> ⚠️ Jika ada error `EACCES`, tambahkan `sudo` di depan command install.

---

## Step 2 — Dapatkan API Keys

### NewsAPI
1. Daftar di [newsapi.org/register](https://newsapi.org/register)
2. Copy API Key dari dashboard

### Google Gemini
1. Buka [aistudio.google.com](https://aistudio.google.com)
2. Klik **"Get API Key"** → **"Create API Key"**
3. Copy API Key

### Telegram Bot
1. Cari **@BotFather** di Telegram
2. Kirim `/newbot` → ikuti instruksi
3. Copy **Bot Token**
4. Kirim `/start` ke bot kamu
5. Buka `https://api.telegram.org/botTOKEN_KAMU/getUpdates`
6. Catat nilai `chat.id`

---

## Step 3 — Import Workflow

1. Buka n8n di `http://localhost:5678`
2. Klik **"New Workflow"**
3. Klik `...` → **"Import from file"**
4. Upload `workflows/main_workflow.json`

---

## Step 4 — Setup Credentials

### HTTP Request Node (NewsAPI)
Tidak perlu credential khusus — API Key sudah include di URL parameter.

Pastikan URL di node HTTP Request adalah:
```
https://newsapi.org/v2/everything?q=stock+market+US&language=en&sortBy=publishedAt&pageSize=10&apiKey=API_KEY_KAMU
```

### HTTP Request1 Node (Gemini)
Pastikan URL adalah:
```
https://generativelanguage.googleapis.com/v1beta/models/gemini-flash-latest:generateContent?key=GEMINI_KEY_KAMU
```

### Telegram Node
1. Klik node Telegram → **"Credential"** → **"Create New"**
2. Masukkan Bot Token
3. Di field **Chat ID**, masukkan ID dari Step 2

---

## Step 5 — Konfigurasi Penting

### Fix Permission Error n8n (Linux/Mac)
Jika node HTTP Request timeout padahal curl berhasil:
```bash
# Stop n8n, lalu jalankan dengan:
export NODE_TLS_REJECT_UNAUTHORIZED=0
n8n start
```

### Retry on Fail
Di node HTTP Request1 (Gemini), aktifkan retry karena API kadang 503:
- Tab **Settings** → **Retry on Fail** → ON
- Max Tries: `3`
- Wait: `5000ms`

---

## Step 6 — Test & Aktifkan

```
1. Klik "Execute Workflow" untuk test manual
2. Cek semua node hijau ✅
3. Cek Telegram — laporan harus masuk
4. Toggle "Active" → ON untuk jadwal otomatis
```

---

## Troubleshooting

| Error | Penyebab | Solusi |
|-------|----------|--------|
| `ETIMEDOUT` di HTTP Request | n8n tidak bisa akses internet | Jalankan dengan `NODE_TLS_REJECT_UNAUTHORIZED=0` |
| `503` dari Gemini | Server overload | Aktifkan Retry on Fail |
| `chat not found` Telegram | Chat ID salah | Cek via `getUpdates` API |
| JSON parse error | Gemini balas dengan teks | Gunakan regex match di Code node |
