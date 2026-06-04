# 📓 Prompt Notes & Changelog
# Konteks: Pasar Saham AS (NYSE / NASDAQ)

---

## Prinsip Utama

1. **Self-critique beats one-shot** — Memaksa model mengkritik dirinya sendiri sebelum output final menghasilkan analisis yang jauh lebih balanced
2. **Role specificity matters** — "Wall Street analyst 20 years" lebih efektif dari "financial analyst" generik
3. **Format constraint wajib** — Tanpa instruksi eksplisit "mulai dengan `{`", Gemini sering tambah kalimat pembuka yang merusak JSON parsing
4. **Failsafe untuk data kurang** — Model harus bisa output `confidence_level: LOW` bukan mengarang analisis

---

## Temuan Nyata dari Testing

### Gemini 1.5 Flash vs Pro
- **Flash**: Parse rate ~95%, lebih cepat, gratis tier lebih besar. Digunakan di production.
- **Pro**: Parse rate ~99%, analisis lebih nuanced. Cocok untuk analisis mendalam per saham.
- Gunakan Flash untuk daily run, Pro untuk analisis deep-dive saat ada event besar (Fed meeting, earnings).

### Error 503 dari Gemini
Terjadi saat server overload — bukan masalah prompt atau API key. Solusi: aktifkan **Retry on Fail** di n8n dengan delay 5 detik.

### JSON Parsing Issue
Gemini kadang balas dengan teks sebelum JSON meski sudah ada instruksi. Solusi: gunakan regex `/\{[\s\S]*\}/` di Code node untuk ekstrak JSON dari teks apapun.

### Konteks Pasar AS
- Prompt yang menyebut "Wall Street", "NYSE", "NASDAQ" menghasilkan analisis yang lebih relevan
- Model lebih akurat untuk saham large cap (AAPL, MSFT, NVDA) vs small cap yang datanya lebih sedikit di training
- Untuk saham airlines (UAL, DAL, ALK), model memahami kaitan dengan harga minyak secara otomatis

---

## Changelog

### v2.1 (Planned)
- [ ] Tambah instruksi: "Prioritaskan saham dengan market cap > $1B"
- [ ] Tambah field `catalyst` — apa event spesifik yang jadi penggerak
- [ ] Few-shot examples dengan kasus earnings beat/miss nyata

### v2.0 (Current) ✅
- [x] Self-critique loop (3 langkah)
- [x] Role: "Wall Street analyst 20 years"
- [x] Output constraint: "mulai dengan '{'"
- [x] Parse rate meningkat dari ~60% ke ~98%

### v1.5
- [x] Tambah role prompting
- [x] Format JSON dasar
- [x] Masih sering ada teks di luar JSON

### v1.0
- [x] Prompt dasar tanpa role
- [x] Output teks bebas, tidak bisa di-parse otomatis
