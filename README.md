# AI Stock Analyzer — n8n + Google Gemini

> Otomatisasi analisis pasar saham AS harian berbasis berita menggunakan **n8n workflow** dan **Google Gemini AI** — memberikan saran investasi otomatis setiap hari langsung ke Telegram.

![n8n](https://img.shields.io/badge/n8n-Workflow-orange?style=flat-square&logo=n8n)
![Gemini](https://img.shields.io/badge/Google-Gemini%20AI-blue?style=flat-square&logo=google)
![Telegram](https://img.shields.io/badge/Telegram-Bot-2CA5E0?style=flat-square&logo=telegram)
![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)
![Status](https://img.shields.io/badge/status-Active-brightgreen?style=flat-square)

---

## Tentang Project Ini

Project ini adalah **automation pipeline** yang bekerja setiap hari secara otomatis untuk:

1. **Mengambil berita** pasar saham AS terkini dari NewsAPI
2. **Menganalisis** berita menggunakan Google Gemini dengan teknik **Self-Critique Prompting**
3. **Memberikan saran** beli / jual / wait and see beserta Top 3 saham terdampak
4. **Mengirim laporan** ringkas ke Telegram secara otomatis setiap hari jam 09.30 ET

Dibuat sebagai portofolio **AI Prompt Engineering & Automation** — menunjukkan kemampuan merancang prompt yang efektif dan membangun pipeline otomatisasi AI end-to-end.

---

## Arsitektur Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                        n8n Workflow                             │
│                                                                 │
│  [Schedule Trigger]  ──── Setiap hari 09.30 ET                 │
│         │                                                       │
│         ▼                                                       │
│  [HTTP Request]  ──── GET NewsAPI (berita saham AS terbaru)    │
│         │                                                       │
│         ▼                                                       │
│  [Code JavaScript]  ──── Bersihkan & gabungkan teks berita     │
│         │                                                       │
│         ▼                                                       │
│  [HTTP Request]  ──── POST Google Gemini API (analisis AI)     │
│         │                                                       │
│         ▼                                                       │
│  [Code JavaScript]  ──── Parse JSON output dari Gemini         │
│         │                                                       │
│         ▼                                                       │
│  [Telegram]  ──── Kirim laporan ke bot Telegram                │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 Struktur Folder

```
ai-stock-analyzer-n8n/
│
├── 📄 README.md                      # Dokumentasi utama (file ini)
│
├── 📂 workflows/
│   └── main_workflow.json            # File workflow n8n (import langsung)
│
├── 📂 prompts/
│   ├── system_prompt.md              # Prompt utama + penjelasan
│   ├── analysis_prompt.md            # Variasi prompt untuk kasus berbeda
│   └── prompt_notes.md               # Catatan iterasi & keputusan prompt
│
├── 📂 docs/
│   ├── SETUP.md                      # Panduan instalasi step-by-step
│   ├── PROMPT_ENGINEERING.md         # Dokumentasi strategi prompt
│   └── EXAMPLE_OUTPUT.md             # Contoh hasil output nyata
│
└── 📂 examples/
    ├── sample_output.md              # Contoh output analisis nyata
    └── sample_news_input.json        # Contoh input berita mentah
```

---

## Fitur Utama

| Fitur | Deskripsi |
|-------|-----------|
| **Auto Schedule** | Berjalan otomatis setiap hari jam 09.30 ET (saat NYSE buka) |
| **Live News** | Ambil 10 berita saham AS terbaru dari NewsAPI |
| **Self-Critique AI** | Gemini analisis → kritik diri sendiri → output final yang lebih tajam |
| **Top 3 Stocks** | Identifikasi 3 saham paling terdampak berita hari ini |
| **Telegram Report** | Laporan dikirim otomatis ke bot Telegram |
| **Prompt Versioning** | Prompt di-track versinya untuk iterasi terdokumentasi |

---

## Highlight: Self-Critique Prompting

Teknik prompt utama yang digunakan adalah **Self-Critique Loop** — Gemini diminta untuk:

```
LANGKAH 1 → Buat analisis awal dari berita
LANGKAH 2 → Kritik analisis sendiri (apa yang bias/kurang akurat?)
LANGKAH 3 → Hasilkan output FINAL berdasarkan hasil kritik
```

Teknik ini menghasilkan analisis yang lebih **objektif dan balanced** dibanding prompt one-shot biasa.

---

## Contoh Output Telegram

```
ANALISIS PASAR SAHAM AS
02 June 2026 | 09:30 ET

━━━━━━━━━━━━━━━━━━━━

Sentimen Pasar: POSITIF
Confidence: HIGH

Ringkasan:
Pasar saham global mencatat rekor tertinggi baru didorong oleh
sentimen positif aktivitas korporasi dan rencana IPO Anthropic,
meskipun ada tekanan dari lonjakan harga minyak akibat ketegangan
dengan Iran.

TOP 3 Saham Terdampak:
1. $AROW — positif | OCC setujui merger dengan Adirondack Bancorp
2. $OCFC — positif | Selesaikan merger + investasi strategis $225M
3. $DSV  — positif | Akuisisi Kidd Operations untuk 2x produksi emas

Saran Tindakan: BELI
Momentum pasar kuat dengan indeks utama di rekor baru, didukung
konsolidasi industri yang sukses dan inovasi sektor AI.

━━━━━━━━━━━━━━━━━━━━
Bukan saran investasi. DYOR.
```

---

## Quick Start

### Prasyarat

- [n8n](https://n8n.io) (self-hosted: `npm install n8n -g`)
- [NewsAPI Key](https://newsapi.org/register) (gratis)
- [Google AI Studio API Key](https://aistudio.google.com) (Gemini)
- Telegram Bot Token (dari [@BotFather](https://t.me/BotFather))

### Instalasi

```bash
# 1. Clone repo
git clone https://github.com/AgungPerdana-IT/ai-stock-analyzer-n8n.git

# 2. Jalankan n8n
n8n start

# 3. Buka http://localhost:5678
# 4. Import workflows/main_workflow.json
# 5. Isi credentials (NewsAPI, Gemini, Telegram)
# 6. Aktifkan workflow
```

> Panduan lengkap ada di [`docs/SETUP.md`](docs/SETUP.md)

---

## Konfigurasi

| Parameter | Lokasi | Nilai Default |
|-----------|--------|---------------|
| Jadwal run | Schedule Trigger node | Setiap hari 09.30 ET |
| Keyword berita | HTTP Request node (NewsAPI URL) | `stock+market+US` |
| Jumlah berita | NewsAPI URL parameter `pageSize` | `10` |
| Model Gemini | HTTP Request1 URL | `gemini-1.5-flash` |

---

## Dokumentasi

| Dokumen | Deskripsi |
|---------|-----------|
| [`docs/SETUP.md`](docs/SETUP.md) | Panduan instalasi detail |
| [`docs/PROMPT_ENGINEERING.md`](docs/PROMPT_ENGINEERING.md) | Strategi & iterasi prompt |
| [`prompts/system_prompt.md`](prompts/system_prompt.md) | Prompt lengkap yang digunakan |

---

## Roadmap

- [x] Workflow dasar: berita → AI → Telegram
- [x] Self-critique prompting untuk analisis lebih tajam
- [x] Output JSON terstruktur
- [ ] Versi saham Indonesia (BEI)
- [ ] Dashboard web histori analisis
- [ ] Integrasi data harga real-time
- [ ] Backtesting akurasi prediksi

---

## Disclaimer

> Project ini dibuat untuk tujuan **edukasi dan portofolio**. Hasil analisis AI **bukan merupakan saran investasi profesional**. Selalu lakukan riset mandiri (DYOR) sebelum mengambil keputusan investasi.

---

## Lisensi

[MIT License](LICENSE)

---

**[Agung Perdana]**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat-square&logo=linkedin)](https://linkedin.com/in/agung-perdana-it)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github)](https://github.com/AgungPerdana-IT)

---
