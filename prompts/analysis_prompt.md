# Analysis Prompt — Template & Variasi
# Untuk pasar saham AS (NYSE / NASDAQ)

---

## Template 1: Analisis Berita Earnings (US Stocks)

Gunakan saat ada berita laporan keuangan (earnings release):

```
Kamu adalah analis saham Wall Street profesional.

Saham {TICKER} baru saja merilis laporan keuangan {QUARTER} {YEAR}.

Data keuangan utama:
{DATA_KEUANGAN}

Berita dan komentar analis:
{BERITA}

LANGKAH 1 - Analisis apakah hasil earnings ini beat, meet, atau miss ekspektasi konsensus.
LANGKAH 2 - Kritik analisis kamu: apakah ada faktor one-time yang perlu dikeluarkan?
LANGKAH 3 - Output FINAL dalam JSON murni:

{"sentimen_pasar":"positif/negatif/netral","ringkasan":"2-3 kalimat","top_3_saham":[{"ticker":"XXXX","dampak":"positif/negatif/netral","alasan":"singkat"}],"saran_tindakan":{"aksi":"beli/jual/wait and see","alasan":"logis"},"confidence_level":"LOW/MEDIUM/HIGH"}
```

---

## Template 2: Analisis Sentimen Makro AS

Gunakan saat berita lebih ke makroekonomi (Fed rate decision, CPI, NFP, dll):

```
Kamu adalah analis makroekonomi dan strategi pasar senior.

Berikut adalah data/berita makroekonomi AS hari ini:
{BERITA_MAKRO}

LANGKAH 1 - Analisis implikasi berita ini terhadap pasar saham AS secara keseluruhan.
LANGKAH 2 - Kritik: sektor mana yang paling terdampak positif dan negatif?
LANGKAH 3 - Output FINAL JSON:

{"sentimen_pasar":"positif/negatif/netral","ringkasan":"2-3 kalimat","top_3_saham":[{"ticker":"XXXX","dampak":"positif/negatif/netral","alasan":"kaitan ke makro"}],"saran_tindakan":{"aksi":"beli/jual/wait and see","alasan":"berbasis data makro"},"confidence_level":"LOW/MEDIUM/HIGH"}
```

---

## Template 3: Prompt Hemat Token (Gemini Flash)

Versi ringkas untuk efisiensi biaya API:

```
Analis saham AS profesional. Berita: {BERITA}

Analisis → Kritik → JSON final:
{"sentimen_pasar":"...","ringkasan":"...","top_3_saham":[{"ticker":"...","dampak":"...","alasan":"..."}],"saran_tindakan":{"aksi":"...","alasan":"..."},"confidence_level":"..."}

HANYA JSON. Mulai dengan '{'.
```

---

## Catatan Penggunaan

- **Template 1** → Earnings season (Jan, Apr, Jul, Oct) untuk saham AS
- **Template 2** → Hari rilis data Fed, CPI, NFP, GDP
- **Template 3** → Gunakan jika budget API terbatas, output lebih simpel

## Contoh Ticker yang Sering Muncul

| Sektor | Ticker Representatif |
|--------|---------------------|
| Tech | AAPL, MSFT, NVDA, GOOGL, META |
| Finance | JPM, BAC, GS, MS, WFC |
| Energy | XOM, CVX, COP |
| Airlines | UAL, DAL, ALK, LUV |
| ETF | SPY, QQQ, KRE, XLE, XLF |
