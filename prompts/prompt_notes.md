# 📓 Prompt Notes & Changelog

Catatan iterasi, keputusan, dan hal-hal yang dipelajari selama pengembangan prompt.

---

## Prinsip Utama yang Dipegang

1. **Specificity beats generality** — Prompt yang spesifik ke konteks Indonesia + BEI jauh lebih baik dari prompt generik
2. **Format constraint adalah wajib** — Tanpa schema JSON yang eksplisit, output tidak bisa diotomatisasi
3. **Failsafe itu penting** — AI harus bisa bilang "data tidak cukup" alih-alih mengarang
4. **Balance is designed, not assumed** — Harus secara eksplisit meminta faktor risiko, tidak bisa mengandalkan model

---

## Temuan Menarik

### Tentang Gemini 1.5 Flash vs Pro
- **Flash**: Parse rate ~92%, lebih cepat, lebih murah. Cocok untuk batch harian.
- **Pro**: Parse rate ~98%, analisis lebih nuanced. Cocok untuk analisis mendalam.
- Untuk production, gunakan Flash dengan fallback ke Pro jika parse gagal.

### Tentang Bahasa
- Prompt dalam **Bahasa Indonesia** menghasilkan output yang lebih relevan untuk konteks lokal
- Nama perusahaan, istilah regulasi (OJK, BI, BEI) lebih tepat diinterpretasikan
- Istilah teknikal keuangan boleh tetap dalam Inggris (NPL, NIM, EBITDA) — model sudah familiar

### Tentang Panjang Berita
- Optimal: 3-5 artikel per saham, masing-masing 1-2 paragraf
- Terlalu sedikit (1 artikel): confidence turun, output kurang nuanced
- Terlalu banyak (10+ artikel): token mahal, kadang model "overwhelmed" dan output jadi generik

---

## Changelog

### v3.1 (Planned)
- [ ] Tambah constraint panjang ringkasan: "maksimal 60 kata"
- [ ] Tambah field `aksi_monitoring`: apa yang perlu dipantau investor

### v3.0 (Current)
- [x] Full RCTSF framework
- [x] Tambah `confidence_level` dan `horizon_waktu`
- [x] Tambah chain-of-thought tersembunyi
- [x] Improved failsafe untuk data minim

### v2.0
- [x] Role prompting sebagai analis Indonesia
- [x] Output JSON dasar
- [x] Pisahkan faktor positif dan risiko

### v1.0
- [x] Prompt baseline (teks bebas)
