# Analysis Prompt — Template & Variasi
# Digunakan untuk kasus analisis spesifik

---

## Template 1: Analisis Berita Earnings

Gunakan saat ada berita laporan keuangan (earnings release):

```
Saham {kode_saham} baru saja merilis laporan keuangan {periode}.

Data keuangan utama:
{data_keuangan}

Berita dan komentar analis:
{berita}

Bandingkan dengan ekspektasi pasar sebelumnya dan beri analisis singkat dampak ke harga saham dalam 1-5 hari ke depan. Output dalam format JSON standar.
```

---

## Template 2: Analisis Sentimen Makro

Gunakan saat berita lebih ke makroekonomi (BI rate, inflasi, dll):

```
Berikut adalah berita makroekonomi Indonesia yang relevan hari ini:
{berita_makro}

Analisis bagaimana berita ini berdampak ke sektor {sektor} secara umum, dan khususnya ke saham {kode_saham}. Pertimbangkan sensitivitas saham ini terhadap perubahan suku bunga / kurs / inflasi.

Output JSON standar.
```

---

## Template 3: Analisis Sentimen Singkat (untuk Gemini Flash)

Versi hemat token untuk model yang lebih kecil:

```
Berita: {berita}
Saham: {kode_saham}

Tugas: Analisis sentimen berita ini terhadap saham di atas.
Output JSON: {"saham":"...","skor_sentimen":<-10 to 10>,"rekomendasi":"BUY/HOLD/SELL","alasan":"...","confidence":"LOW/MEDIUM/HIGH"}
HANYA JSON, tanpa teks lain.
```

---

## Catatan Penggunaan

- **Template 1** → Cocok untuk hari-hari sekitar RUPS atau earnings season
- **Template 2** → Gunakan saat ada event makro besar (rapat BI, data inflasi, dll)  
- **Template 3** → Gunakan jika budget API terbatas, hasilnya lebih simpel
