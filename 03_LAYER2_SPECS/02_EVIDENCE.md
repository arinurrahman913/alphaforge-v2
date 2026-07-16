# Evidence

**Status:** Aktif — revisi lengkap dengan detail sumber data

## Definisi

Evidence adalah fakta mentah yang sudah diverifikasi tentang **satu saham spesifik**. Ini murni pengumpulan fakta — belum ada interpretasi, belum ada skor, belum ada kesimpulan. Beda dari data Layer 1 yang sifatnya makro/market-wide, Evidence selalu spesifik per ticker.

## Kenapa Penting

Evidence adalah fondasi faktual yang semua reasoning berikutnya dibangun di atasnya (Prinsip #1: Evidence Before Knowledge, `00_Foundation/02_PRINCIPLES.md`). Kualitas Knowledge dan reasoning di tahap berikutnya sangat bergantung pada kelengkapan dan keandalan Evidence di sini.

---

## 1. Kategori Data & Sumbernya

### 1.1 Harga & Data Pasar
**Sumber:** Yahoo Finance (`yfinance`)
- Harga historis (OHLCV — open, high, low, close, volume)
- Statistik pasar dasar: market cap, shares outstanding, beta
- Ini sudah dipakai di v1 (`providers/yahoo_finance.py`), kode-nya bisa dipakai ulang.

### 1.2 Data Fundamental / Keuangan
**Sumber utama:** Yahoo Finance — ringkasan fundamental (revenue, net income, EPS, margin, balance sheet ringkas, cash flow ringkas). Cepat diakses, tapi levelnya ringkasan.

**Sumber pelengkap (lebih detail & resmi):** SEC EDGAR — laporan keuangan resmi lengkap (10-K tahunan, 10-Q kuartalan). Dipakai kalau Knowledge butuh detail yang tidak tersedia di ringkasan Yahoo Finance (misal breakdown revenue per segmen bisnis). Sudah ada provider-nya di v1 (`providers/sec_edgar.py`).

### 1.3 Kepemilikan Institusional
Ada dua tingkat data, dengan jalur gratis yang berbeda:

**Persentase kepemilikan institusional (agregat):**
Tersedia gratis langsung dari Yahoo Finance (`institutional_ownership_pct`) — cukup untuk field agregat di Knowledge tanpa perlu parsing filing.

**Detail per-institusi (13F holder-by-holder) — SEC EDGAR langsung:**
Sumber paling otoritatif dan gratis. Setiap dana kelolaan besar (di atas ambang tertentu) wajib melaporkan kepemilikan sahamnya ke SEC tiap kuartal lewat form 13F. Datanya resmi dan gratis, tapi berupa filing mentah yang perlu diparsing sendiri.

**Finnhub (`institutional-ownership`) — TIDAK dipakai di jalur gratis.**
Endpoint ini sempat diasumsikan gratis (merangkum 13F/13D/13G jadi format siap pakai), tapi testing implementasi mengonfirmasi endpoint ini **premium-only — mengembalikan HTTP 403 di free tier**, bukan 60 calls/menit tanpa kartu kredit seperti asumsi awal. Karena itu ia **bukan** sumber institutional ownership untuk v2 selama masih memegang Prinsip #7 (gratis dulu). Baru dipertimbangkan kalau nanti ada anggaran berbayar dan parsing EDGAR terbukti tidak memadai.

**Pendekatan yang disarankan:** persentase agregat dari Yahoo Finance untuk kebutuhan dasar; detail holder-by-holder diparsing dari SEC EDGAR 13F kalau modul reasoning benar-benar membutuhkannya. Kalau EDGAR tidak menyediakan detail untuk ticker tertentu, field detail-nya diisi `null` + status `missing` (lihat `03_KNOWLEDGE.md`), bukan ditebak.

### 1.4 Berita Perusahaan
**Sumber:** Finnhub — endpoint `company-news`, mengembalikan berita yang sudah difilter per ticker dengan rentang tanggal.

Dibanding scraping situs berita (berisiko dari sisi ToS) atau layanan berbayar, endpoint ini sudah terstruktur dan lebih enak dipakai untuk skala personal/prototipe.

> **Catatan verifikasi (penting):** ketersediaan free tier Finnhub harus dicek per endpoint sebelum diandalkan. Endpoint `institutional-ownership` sudah terbukti premium-only (403) meski awalnya diasumsikan gratis (lihat 1.3). Jadi `company-news` juga wajib dikonfirmasi masih gratis saat implementasi; kalau ternyata premium juga, fallback-nya adalah berita/press release resmi dari SEC EDGAR (8-K) dan sumber gratis lain, bukan langsung pindah ke layanan berbayar.

### 1.5 SEC Filings Umum (Non-13F)
**Sumber utama:** SEC EDGAR langsung — filing resmi (8-K, 10-K, 10-Q, dll) gratis dan otoritatif, sudah ada provider-nya di v1 (`sec_edgar.py`). Berguna kalau Risk/Red-Flag Check (`04_RISK_REDFLAG_CHECK.md`) butuh mendeteksi filing tidak biasa (misal restatement, pergantian auditor).

Finnhub bisa jadi lapisan kemudahan di atas EDGAR, tapi dengan catatan verifikasi free-tier yang sama seperti di 1.4 — EDGAR yang dijadikan sumber gratis yang dijamin, bukan Finnhub.

---

## 2. Ringkasan Sumber

| Kategori Data | Sumber Utama | Sumber Pelengkap | Status |
|---|---|---|---|
| Harga & pasar | Yahoo Finance | — | Gratis |
| Fundamental ringkas | Yahoo Finance | — | Gratis |
| Fundamental detail (10-K/10-Q) | SEC EDGAR | — | Gratis |
| Kepemilikan institusional (persentase) | Yahoo Finance | — | Gratis |
| Kepemilikan institusional (detail 13F) | SEC EDGAR 13F (raw, parse sendiri) | — | Gratis. Finnhub `institutional-ownership` **premium-only (403)** — tidak dipakai |
| Berita perusahaan | Finnhub (`company-news`) | SEC EDGAR (8-K) | Free tier **perlu diverifikasi per endpoint** (lihat 1.4) |
| SEC filings umum | SEC EDGAR | Finnhub (jika free tier terkonfirmasi) | Gratis (EDGAR) |

**Catatan rate limit:** kalau Finnhub tetap dipakai untuk endpoint yang terkonfirmasi gratis, free tier-nya dibatasi 60 request/menit dan perlu masuk ke strategi batching & caching yang sama seperti Yahoo Finance — lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.

---

## 3. Cara Kerja (Alur Pengumpulan)

1. Ambil daftar ticker kandidat dari hasil Screening.
2. Untuk tiap ticker: tarik harga & fundamental ringkas dari Yahoo Finance.
3. Tarik persentase kepemilikan institusional dari Yahoo Finance; kalau modul reasoning butuh detail per-institusi, parsing 13F dari SEC EDGAR (Finnhub `institutional-ownership` premium-only, tidak dipakai).
4. Tarik berita terkini dari Finnhub (`company-news`) — setelah free tier endpoint ini terkonfirmasi — rentang waktu yang relevan (misal 30-90 hari terakhir); fallback ke 8-K SEC EDGAR kalau tidak tersedia gratis.
5. Kalau Knowledge nanti butuh detail fundamental lebih dalam dari yang tersedia di ringkasan, tarik 10-K/10-Q dari SEC EDGAR secara spesifik.
6. Setiap data yang masuk ditandai dengan **sumber** dan **waktu pengambilan (timestamp)** — ini jadi bahan baku untuk Confidence/Data Quality Score di tahap berikutnya.

## 4. Yang Masih Perlu Diputuskan

- **Field wajib minimum** per kategori — misal berapa tahun histori harga yang ditarik, apakah utang jangka pendek/panjang dipisah di level Evidence atau baru dipisah di Knowledge.
- **Format penyimpanan metadata sumber** — apakah tiap field data punya metadata `{source, fetched_at}` sendiri, atau cukup di level keseluruhan objek Evidence per ticker.
- **Ambang waktu "berita relevan"** — 30 hari, 90 hari, atau disesuaikan per modul (Speculative kemungkinan butuh jendela lebih pendek dibanding Quality/Compound).

Detail ini didiskusikan lebih lanjut mendekati fase implementasi.

## Terhubung Dengan

Knowledge (menerima Evidence sebagai input), Confidence/Data Quality (menilai kelengkapan Evidence berdasarkan metadata sumber di sini), Catalyst Tracking (memakai data berita & filing untuk mengidentifikasi peristiwa mendatang).

---

© AlphaForge v2
