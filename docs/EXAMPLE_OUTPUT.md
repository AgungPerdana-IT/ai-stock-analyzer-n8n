# 📊 Contoh Output Nyata

Kumpulan contoh output dari sistem yang sudah berjalan. Digunakan untuk validasi kualitas prompt dan sebagai referensi.

---

## Hasil Run — 15 Januari 2025

| Saham | Rekomendasi | Skor | Confidence | Parse ✓ |
|-------|------------|------|------------|---------|
| BBCA | BUY | +7 | HIGH | ✅ |
| TLKM | HOLD | +3 | MEDIUM | ✅ |
| GOTO | BUY | +6 | MEDIUM | ✅ |
| BMRI | HOLD | +2 | LOW | ✅ |
| ASII | SELL | -4 | MEDIUM | ✅ |

**Parse Rate:** 5/5 (100%) ✅

---

## Detail Output: GOTO

```json
{
  "saham": "GOTO",
  "tanggal": "15 Januari 2025",
  "skor_sentimen": 6,
  "rekomendasi": "BUY",
  "ringkasan": "GoTo Group mencapai milestone penting dengan membukukan adjusted EBITDA positif untuk pertama kalinya di Q4 2024. Pencapaian ini menjadi sinyal kuat bahwa strategi efisiensi perusahaan mulai membuahkan hasil, meskipun profitabilitas GAAP masih membutuhkan waktu.",
  "faktor_positif": [
    "EBITDA positif pertama kali merupakan katalis sentimen yang signifikan",
    "Menandai titik balik dalam narasi 'path to profitability' yang selama ini diragukan pasar",
    "Potensi re-rating valuasi jika tren berlanjut di Q1 2025"
  ],
  "faktor_risiko": [
    "EBITDA adjusted berbeda dengan profitabilitas GAAP yang sesungguhnya — perlu kehati-hatian dalam interpretasi",
    "Persaingan ketat dengan Shopee dan platform regional lain di segmen e-commerce",
    "Saham dengan volatilitas tinggi, tidak cocok untuk investor konservatif"
  ],
  "confidence_level": "MEDIUM",
  "horizon_waktu": "SHORT",
  "berita_utama": "GOTO Klaim Catat EBITDA Positif untuk Pertama Kali di Q4 2024",
  "catatan": "Perlu verifikasi metodologi perhitungan EBITDA yang digunakan manajemen vs standar analis",
  "disclaimer": "Analisis ini dibuat oleh AI berdasarkan berita tersedia dan bukan merupakan saran investasi profesional. Selalu lakukan riset mandiri sebelum berinvestasi."
}
```

---

## Catatan Kualitas Output

### Yang Berjalan Baik ✅
- Semua output berhasil di-parse sebagai valid JSON
- `faktor_risiko` selalu terisi (tidak pernah array kosong)
- `confidence_level` bervariasi — tidak semua HIGH
- `disclaimer` konsisten di semua output

### Area Perbaikan 🔧
- Beberapa analisis GOTO terlalu panjang (>200 kata di ringkasan)
- Perlu tambah constraint panjang maksimum di prompt
- Saham dengan berita sangat minim kadang masih BUY (bukan INSUFFICIENT_DATA)

### Rencana Perbaikan
Iterasi prompt v3.1: tambahkan `"ringkasan": "<maksimal 60 kata>"` di schema
