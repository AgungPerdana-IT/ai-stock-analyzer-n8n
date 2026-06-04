# 🧠 Prompt Engineering — Dokumentasi & Strategi

Dokumen ini menjelaskan bagaimana dan mengapa prompt dirancang seperti ini.

---

## Teknik Utama: Self-Critique Loop

Prompt menggunakan teknik **Self-Refinement Prompting**:

```
LANGKAH 1 → Analisis awal
LANGKAH 2 → Kritik diri sendiri
LANGKAH 3 → Output final berdasarkan kritik
```

**Kenapa efektif?**
LLM cenderung overconfident pada output pertama. Dengan memaksa model mengkritik dirinya sendiri, analisis menjadi lebih **objektif, balanced, dan akurat**.

---

## Anatomi Prompt (Framework RCSF)

| Komponen | Isi | Tujuan |
|----------|-----|--------|
| **Role** | "analis saham profesional Wall Street 20 tahun" | Membangun expertise frame |
| **Context** | Berita hari ini dari NewsAPI | Grounding ke data nyata |
| **Steps** | 3 langkah self-critique | Mendorong reasoning yang lebih dalam |
| **Format** | JSON schema eksplisit | Output konsisten, bisa di-parse otomatis |

---

## Iterasi Prompt

### v1.0 — Baseline (Gagal)
```
Analisis berita ini dan beri saran saham.
Berita: {news}
```
**Masalah:** Output tidak konsisten, tidak bisa di-parse, terlalu spekulatif.

---

### v1.5 — Tambah Role + JSON
```
Kamu adalah analis saham. Balas dalam JSON:
{"rekomendasi": "...", "alasan": "..."}
```
**Masalah:** Gemini masih sering tambah teks di luar JSON, merusak parsing.

---

### v2.0 — Self-Critique + Constraint Ketat ✅
```
Kamu adalah analis saham profesional Wall Street...
LANGKAH 1 - Analisis awal
LANGKAH 2 - Kritik analisis sendiri
LANGKAH 3 - Output FINAL dalam JSON murni, mulai dengan '{'
```
**Hasil:** Parse rate ~98%, analisis lebih balanced, confidence lebih akurat.

---

## Keputusan Desain

### Kenapa JSON output?
Agar hasil analisis bisa langsung diproses n8n untuk formatting pesan Telegram yang rapi — tanpa manual parsing teks bebas.

### Kenapa ada instruksi "mulai dengan `{`"?
Gemini kadang menambahkan kalimat pembuka seperti *"Berikut adalah analisis saya..."* sebelum JSON. Instruksi eksplisit ini menekan perilaku tersebut.

### Kenapa self-critique, bukan langsung output?
Analisis satu langkah cenderung bias ke sentimen berita yang paling dominan. Self-critique memaksa model mempertimbangkan sisi lain sebelum kesimpulan final.

---

## Metrik Kualitas

| Metrik | v1.0 | v2.0 |
|--------|------|------|
| Parse rate (valid JSON) | ~60% | ~98% |
| Selalu ada faktor risiko | ❌ | ✅ |
| Confidence bervariasi | ❌ | ✅ |
| Analisis balanced | ❌ | ✅ |

---

## Rencana Iterasi Selanjutnya

- [ ] Few-shot examples — tambah 2-3 contoh analisis nyata di prompt
- [ ] Pisahkan system prompt dan user prompt untuk kontrol lebih baik
- [ ] Versi untuk saham Indonesia (BEI) dengan konteks OJK dan rupiah
