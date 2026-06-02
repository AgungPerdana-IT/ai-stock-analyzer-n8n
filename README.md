# 📈 AI Stock Analyzer — n8n + Google Gemini

> Otomatisasi analisis saham harian berbasis berita menggunakan **n8n workflow** dan **Google Gemini AI** — memberikan saran investasi yang tajam dan kontekstual secara otomatis setiap hari.

![n8n](https://img.shields.io/badge/n8n-Workflow-orange?style=flat-square&logo=n8n)
![Gemini](https://img.shields.io/badge/Google-Gemini%20AI-blue?style=flat-square&logo=google)
![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)
![Status](https://img.shields.io/badge/status-Active-brightgreen?style=flat-square)

---

## 🎯 Tentang Project Ini

Project ini adalah **automation pipeline** yang bekerja setiap hari untuk:

1. **Mengumpulkan berita** finansial terkini dari berbagai sumber (RSS feed, NewsAPI, dll)
2. **Menganalisis sentimen** berita terhadap saham tertentu menggunakan Google Gemini
3. **Memberikan saran** beli / tahan / jual berdasarkan konteks berita hari ini
4. **Mengirim laporan** ringkas ke email atau Telegram secara otomatis

Dibuat sebagai portofolio **AI Prompt Engineering & Automation** — menunjukkan kemampuan merancang prompt yang efektif untuk use case dunia nyata.

---

## 🏗️ Arsitektur Sistem

```
┌─────────────────────────────────────────────────────────┐
│                    n8n Workflow                         │
│                                                         │
│  [Cron Trigger]                                         │
│       │                                                 │
│       ▼                                                 │
│  [Fetch News]  ──── RSS / NewsAPI / Google News        │
│       │                                                 │
│       ▼                                                 │
│  [Filter & Clean]  ──── Hanya berita relevan           │
│       │                                                 │
│       ▼                                                 │
│  [Gemini AI]   ──── Analisis + Saran (Prompt Engine)   │
│       │                                                 │
│       ▼                                                 │
│  [Format Output]  ──── Laporan terstruktur             │
│       │                                                 │
│       ▼                                                 │
│  [Send Report]  ──── Email / Telegram / Slack          │
└─────────────────────────────────────────────────────────┘
```

---

## 📁 Struktur Folder

```
ai-stock-analyzer-n8n/
│
├── 📄 README.md                    # Dokumentasi utama (file ini)
│
├── 📂 workflows/
│   ├── main_workflow.json          # File workflow n8n utama (import langsung)
│   └── workflow_screenshot.png    # Screenshot tampilan workflow di n8n
│
├── 📂 prompts/
│   ├── system_prompt.md           # System prompt utama untuk Gemini
│   ├── analysis_prompt.md         # Template prompt analisis saham
│   └── prompt_notes.md            # Catatan iterasi & keputusan prompt
│
├── 📂 docs/
│   ├── SETUP.md                   # Panduan instalasi step-by-step
│   ├── PROMPT_ENGINEERING.md      # Dokumentasi strategi prompt
│   └── EXAMPLE_OUTPUT.md         # Contoh hasil output nyata
│
├── 📂 examples/
│   ├── sample_output_BBCA.md      # Contoh output saham BBCA
│   ├── sample_output_TLKM.md      # Contoh output saham TLKM
│   └── sample_news_input.json     # Contoh input berita mentah
│
└── 📂 assets/
    ├── demo.gif                   # Demo GIF alur kerja
    └── architecture.png           # Diagram arsitektur
```

---

## ✨ Fitur Utama

| Fitur | Deskripsi |
|-------|-----------|
| 🕐 **Scheduled Run** | Berjalan otomatis setiap hari jam 08.00 WIB (sebelum market buka) |
| 📰 **Multi-source News** | Mengambil berita dari Google News, RSS IDX, dan sumber lokal |
| 🧠 **AI Analysis** | Gemini menganalisis sentimen dan dampak berita ke harga saham |
| 📊 **Scoring System** | Skor sentimen -10 sampai +10 per saham |
| 📬 **Auto Delivery** | Laporan dikirim otomatis ke Telegram/Email |
| 🔁 **Prompt Versioning** | Prompt di-track versinya untuk iterasi yang terdokumentasi |

---

## 🚀 Quick Start

### Prasyarat

- [n8n](https://n8n.io) (self-hosted atau cloud)
- Google AI Studio API Key (Gemini)
- NewsAPI Key *(opsional, untuk sumber berita tambahan)*

### Langkah Instalasi

```bash
# 1. Clone repository ini
git clone https://github.com/AgungPerdana-IT/ai-stock-analyzer-n8n.git
cd ai-stock-analyzer-n8n

# 2. Buka n8n kamu
# 3. Import file workflow
#    Settings → Import Workflow → pilih workflows/main_workflow.json

# 4. Isi credentials di n8n:
#    - Google Gemini API Key
#    - Telegram Bot Token (opsional)
#    - SMTP / Email credentials (opsional)
```

> 📖 Panduan lengkap ada di [`docs/SETUP.md`](docs/SETUP.md)

---

## 🧠 Prompt Engineering Highlight

Inti dari project ini ada di **desain prompt**-nya. Berikut pendekatan yang digunakan:

### Strategi Prompt

```
ROLE        → Gemini berperan sebagai analis saham berpengalaman
CONTEXT     → Diberi berita hari ini + data historis singkat
TASK        → Analisis dampak berita ke harga saham
CONSTRAINT  → Jawaban harus terstruktur, tidak spekulatif berlebihan
FORMAT      → Output dalam JSON terstruktur yang bisa di-parse n8n
```

### Kenapa Pendekatan Ini Efektif?

1. **Role prompting** → Meningkatkan kualitas analisis finansial
2. **Output format constraint** → JSON output memudahkan otomatisasi
3. **Uncertainty acknowledgment** → Prompt meminta AI mengakui ketidakpastian
4. **Chain of thought** → Gemini diminta menjelaskan reasoning-nya

> 📖 Detail lengkap ada di [`docs/PROMPT_ENGINEERING.md`](docs/PROMPT_ENGINEERING.md)

---

## 📊 Contoh Output

```json
{
  "saham": "BBCA",
  "tanggal": "2025-01-15",
  "skor_sentimen": 7,
  "rekomendasi": "BUY",
  "ringkasan": "BCA mencatatkan pertumbuhan kredit 12% YoY berdasarkan laporan Q4...",
  "faktor_positif": [
    "Pertumbuhan kredit melampaui ekspektasi analis",
    "NIM stabil di tengah tekanan suku bunga"
  ],
  "faktor_risiko": [
    "Potensi kenaikan NPL di segmen UMKM"
  ],
  "confidence_level": "MEDIUM-HIGH",
  "disclaimer": "Bukan saran investasi profesional. DYOR."
}
```

> 📖 Lebih banyak contoh di folder [`examples/`](examples/)

---

## ⚙️ Konfigurasi

Semua konfigurasi dilakukan melalui **n8n Environment Variables** atau langsung di node:

| Variable | Deskripsi | Contoh |
|----------|-----------|--------|
| `GEMINI_API_KEY` | Google AI Studio API Key | `AIza...` |
| `TARGET_STOCKS` | Daftar saham yang dianalisis | `BBCA,TLKM,GOTO` |
| `NEWS_LANGUAGE` | Bahasa filter berita | `id` atau `en` |
| `REPORT_CHANNEL` | Tujuan pengiriman laporan | `telegram` atau `email` |

---

## 📚 Dokumentasi Lengkap

| Dokumen | Deskripsi |
|---------|-----------|
| [`docs/SETUP.md`](docs/SETUP.md) | Panduan instalasi detail |
| [`docs/PROMPT_ENGINEERING.md`](docs/PROMPT_ENGINEERING.md) | Strategi & iterasi prompt |
| [`docs/EXAMPLE_OUTPUT.md`](docs/EXAMPLE_OUTPUT.md) | Kumpulan output nyata |
| [`prompts/system_prompt.md`](prompts/system_prompt.md) | System prompt yang digunakan |

---

## 🗺️ Roadmap

- [x] Workflow dasar analisis berita → saham
- [x] Prompt system dengan output JSON terstruktur
- [x] Pengiriman laporan via Telegram
- [ ] Dashboard web untuk melihat histori analisis
- [ ] Integrasi data harga real-time (Yahoo Finance / IDX API)
- [ ] Backtesting — seberapa akurat prediksi vs kenyataan
- [ ] Multi-bahasa support (EN/ID)

---

## 🤝 Kontribusi

Pull request dan feedback sangat welcome! Kalau kamu punya ide untuk memperbaiki prompt atau workflow, silakan buka [Issue](https://github.com/AgungPerdana-IT/ai-stock-analyzer-n8n/issues) dulu.

---

## ⚠️ Disclaimer

> Project ini dibuat untuk tujuan **edukasi dan portofolio**. Hasil analisis AI **bukan merupakan saran investasi profesional**. Selalu lakukan riset mandiri (DYOR) sebelum mengambil keputusan investasi.

---

## 📄 Lisensi

[MIT License](LICENSE) — bebas digunakan dan dimodifikasi dengan atribusi.

---

<div align="center">
## 👤 Author

**[Agung Perdana]**
QA Engineer | Manual & Automation Testing

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat-square&logo=linkedin)](https://linkedin.com/in/agung-perdana-it)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github)](https://github.com/AgungPerdana-IT)

</div>
