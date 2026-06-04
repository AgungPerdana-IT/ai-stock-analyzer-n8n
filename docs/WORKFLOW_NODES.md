# 🔧 Penjelasan Setiap Node

Dokumentasi detail setiap node dalam workflow n8n.

---

## Gambaran Umum

```
[Schedule Trigger] → [HTTP Request] → [Code JS] → [HTTP Request1] → [Code JS1] → [Telegram]
```

---

## Node 1: Schedule Trigger ⏰

**Fungsi:** Pemicu otomatis — memulai seluruh workflow sesuai jadwal.

**Konfigurasi:**
- Trigger Interval: `Days`
- Days Between Triggers: `1`
- Trigger at Hour: `9`
- Trigger at Minute: `30`

**Catatan:** n8n berjalan di timezone `America/New_York (UTC-04:00)`, sehingga jam 9:30 langsung sesuai dengan waktu pembukaan NYSE tanpa perlu konversi.

---

## Node 2: HTTP Request (NewsAPI) 🌐

**Fungsi:** Mengambil berita pasar saham AS terbaru dari NewsAPI.

**Method:** `GET`

**URL:**
```
https://newsapi.org/v2/everything?q=stock+market+US&language=en&sortBy=publishedAt&pageSize=10&apiKey=YOUR_KEY
```

**Parameter penting:**
- `q=stock+market+US` → keyword pencarian
- `language=en` → filter bahasa Inggris
- `sortBy=publishedAt` → urut dari terbaru
- `pageSize=10` → ambil 10 artikel

**Output:** JSON berisi array `articles` dengan field title, source, description, content.

---

## Node 3: Code in JavaScript `{}` (Parser Berita)

**Fungsi:** Membersihkan dan menggabungkan semua artikel menjadi satu teks untuk dikirim ke Gemini.

**Yang dilakukan:**
1. Loop semua artikel dari NewsAPI
2. Gabungkan title + source + description + content
3. Escape karakter khusus (`\n`, `"`) yang bisa merusak JSON body

**Output:** Field `news_text` berisi semua berita dalam satu string bersih.

**Code:**
```javascript
const response = $input.first().json;
const articles = response.articles;

let newsText = "";

for (let i = 0; i < articles.length; i++) {
  const article = articles[i];
  newsText += `BERITA ${i + 1}: `;
  newsText += `Judul: ${article.title}. `;
  newsText += `Sumber: ${article.source.name}. `;
  newsText += `Deskripsi: ${article.description}. `;
  newsText += `Konten: ${article.content}. `;
  newsText += ` --- `;
}

const cleanText = newsText
  .replace(/\\/g, '')
  .replace(/"/g, "'")
  .replace(/\n/g, ' ')
  .replace(/\r/g, ' ');

return [{ json: { news_text: cleanText, total_articles: articles.length } }];
```

---

## Node 4: HTTP Request1 (Google Gemini) 🧠

**Fungsi:** Mengirim berita ke Gemini untuk dianalisis menggunakan Self-Critique Prompting.

**Method:** `POST`

**URL:**
```
https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=YOUR_KEY
```

**Body Type:** `Raw` → `application/json`

**Prompt Strategy:** Self-Critique Loop (lihat `docs/PROMPT_ENGINEERING.md`)

**Settings:** Retry on Fail = ON, Max Tries = 3 (karena Gemini API kadang 503)

---

## Node 5: Code in JavaScript1 `{}` (Parser Gemini Output)

**Fungsi:** Mengekstrak JSON dari response Gemini dan mengubahnya menjadi object yang bisa diakses node selanjutnya.

**Challenge:** Gemini kadang masih menambahkan teks di luar JSON — regex digunakan untuk mengekstrak hanya bagian `{...}`.

**Code:**
```javascript
const response = $input.first().json;
const rawText = response.candidates[0].content.parts[0].text;

// Ekstrak JSON dengan regex (antisipasi teks tambahan dari Gemini)
const jsonMatch = rawText.match(/\{[\s\S]*\}/);
if (!jsonMatch) throw new Error("Tidak ada JSON ditemukan di output Gemini");

const analysis = JSON.parse(jsonMatch[0]);
return [{ json: analysis }];
```

---

## Node 6: Send a Text Message (Telegram) ✈️

**Fungsi:** Memformat dan mengirim hasil analisis ke Telegram bot.

**Parse Mode:** `Markdown` (untuk bold, italic, formatting)

**Template pesan:** Menggunakan n8n expressions `{{ $json.field }}` untuk inject data dinamis dari output Gemini.
