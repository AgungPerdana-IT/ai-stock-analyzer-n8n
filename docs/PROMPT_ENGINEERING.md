# 🧠 Prompt Engineering — Dokumentasi & Strategi

Dokumen ini menjelaskan **bagaimana dan mengapa** prompt dirancang seperti ini. Ini adalah bagian terpenting dari project — menunjukkan proses berpikir di balik otomatisasi AI.

---

## 🎯 Tujuan Prompt

Prompt harus bisa membuat Gemini:

1. **Memahami konteks** saham Indonesia (BEI, regulasi OJK, dll)
2. **Menganalisis berita** secara objektif tanpa bias berlebihan
3. **Memberikan saran** yang actionable tapi tetap prudent
4. **Output JSON** yang konsisten agar bisa di-parse n8n
5. **Mengakui ketidakpastian** — tidak overpromise

---

## 🏗️ Struktur Prompt (Framework: RCTSF)

Prompt utama menggunakan framework **RCTSF**:

| Komponen | Singkatan | Fungsi |
|----------|-----------|--------|
| **Role** | R | Mendefinisikan persona AI |
| **Context** | C | Memberikan latar belakang situasi |
| **Task** | T | Instruksi tugas yang spesifik |
| **Schema** | S | Format output yang diharapkan |
| **Failsafe** | F | Instruksi jika data tidak cukup |

---

## 📝 Anatomi System Prompt

```
[ROLE]
Kamu adalah analis saham berpengalaman di pasar modal Indonesia...
↳ Tujuan: Membangun "expertise frame" agar output lebih relevan

[CONTEXT]  
Pasar yang kamu analisis adalah Bursa Efek Indonesia (BEI)...
↳ Tujuan: Grounding ke konteks lokal, bukan pasar global generik

[TASK]
Berdasarkan berita yang diberikan, analisis dampaknya...
↳ Tujuan: Instruksi eksplisit, tidak ambigu

[SCHEMA]
Berikan output HANYA dalam format JSON berikut...
↳ Tujuan: Konsistensi output untuk otomatisasi

[FAILSAFE]
Jika berita tidak cukup untuk analisis, kembalikan...
↳ Tujuan: Mencegah hallucination atau output rusak
```

---

## 🔄 Iterasi Prompt (Version History)

### v1.0 — Prompt Awal (Baseline)

```
Analisis berita berikut dan beri tahu apakah saham ini layak dibeli.
Berita: {news}
Saham: {stock}
```

**Masalah yang ditemukan:**
- ❌ Output tidak konsisten (kadang paragraf, kadang list)
- ❌ Sering overly bullish, tidak mengakui risiko
- ❌ Tidak bisa di-parse otomatis oleh n8n
- ❌ Tidak ada context pasar Indonesia

---

### v2.0 — Tambah Role + Format JSON

```
Kamu adalah analis saham profesional. 
Analisis berita berikut tentang {stock} dan berikan output dalam JSON:
{
  "rekomendasi": "BUY/HOLD/SELL",
  "alasan": "..."
}
Berita: {news}
```

**Improvement:**
- ✅ Output mulai konsisten
- ✅ JSON bisa di-parse

**Masalah yang tersisa:**
- ❌ JSON kadang ada teks tambahan sebelum/sesudah `{}`
- ❌ Masih terlalu simpel, tidak ada nuansa
- ❌ Belum ada confidence level

---

### v3.0 — Full Framework (Versi Saat Ini)

Versi lengkap ada di `prompts/system_prompt.md`

**Perubahan kunci:**
- ✅ Explicit instruction: *"Balas HANYA dengan JSON, tanpa teks lain"*
- ✅ Tambah `confidence_level` untuk mengukur keyakinan AI
- ✅ Tambah `faktor_risiko` — mendorong balanced analysis
- ✅ Tambah `disclaimer` field di dalam JSON
- ✅ Chain-of-thought tersembunyi: *"Sebelum menjawab, pertimbangkan..."*

---

## 💡 Keputusan Desain & Alasannya

### 1. Kenapa output JSON, bukan teks biasa?

```
MASALAH: Teks bebas susah di-parse otomatis
SOLUSI: JSON dengan schema tetap

TRADEOFF: Model kadang keluar dari format
MITIGASI: Explicit instruction + error handling di n8n
```

### 2. Kenapa ada `confidence_level`?

Pasar saham inherently uncertain. Memaksa AI mengakui ketidakpastian:
- Mencegah pengguna over-rely pada output
- Membuat output lebih jujur dan trustworthy
- Berguna untuk filtering: hanya act jika confidence HIGH

### 3. Kenapa pisahkan `faktor_positif` dan `faktor_risiko`?

```
Tanpa pemisahan → AI cenderung bias ke satu arah
Dengan pemisahan → Forced balanced analysis
```

Teknik ini disebut **"perspective forcing"** — memaksa model mempertimbangkan kedua sisi.

### 4. Kenapa ada instruksi "jangan spekulasi berlebihan"?

LLM cenderung confident bahkan saat data kurang. Instruksi eksplisit:
> *"Jika berita tidak memberikan cukup informasi, nyatakan ketidakpastian daripada berspekulasi"*

Ini meningkatkan **reliability** output untuk use case finansial.

---

## 📏 Metrik Kualitas Prompt

Cara mengukur apakah prompt berhasil:

| Metrik | Cara Ukur | Target |
|--------|-----------|--------|
| **Parse Rate** | % output yang valid JSON | > 95% |
| **Balanced Analysis** | Selalu ada faktor risiko | 100% |
| **Appropriate Confidence** | Tidak semua HIGH | < 40% HIGH |
| **Relevance** | Berita relevan ke saham | Manual check |

---

## 🚀 Rencana Iterasi Selanjutnya

- [ ] **Few-shot examples** — Tambahkan 2-3 contoh analisis nyata di system prompt
- [ ] **Retrieval-Augmented** — Inject data harga historis ke context
- [ ] **Multi-step reasoning** — Pisahkan step analisis sentimen dan step rekomendasi
- [ ] **Self-consistency** — Jalankan prompt 3x, ambil majority vote
