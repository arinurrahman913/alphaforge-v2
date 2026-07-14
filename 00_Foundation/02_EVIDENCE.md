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
Ada dua jalur, keduanya gratis:

**Jalur A — SEC EDGAR langsung (13F filings):**
Sumber paling otoritatif. Setiap dana kelolaan besar (di atas ambang tertentu) wajib melaporkan kepemilikan sahamnya ke SEC tiap kuartal lewat form 13F. Datanya resmi dan gratis, tapi berupa filing mentah yang perlu diparsing sendiri.

**Jalur B — Finnhub (data 13F yang sudah dirapikan):**
Finnhub menyediakan endpoint `institutional-ownership` yang sudah merangkum data 13F/13D/13G menjadi format siap pakai — nama institusi, jumlah saham dimiliki, perubahan dari kuartal sebelumnya. Free tier: 60 API calls/menit, tanpa kartu kredit. Ini mempercepat pengembangan dibanding parsing manual dari EDGAR.

**Pendekatan yang disarankan:** pakai Finnhub sebagai sumber utama (lebih cepat diintegrasikan), dengan SEC EDGAR sebagai verifikasi silang / fallback kalau data Finnhub tidak lengkap untuk ticker tertentu.

### 1.4 Berita Perusahaan
**Sumber:** Finnhub — endpoint `company-news`, gratis di free tier, mengembalikan berita yang sudah difilter per ticker dengan rentang tanggal.

Ini menutup gap yang sebelumnya belum jelas di versi awal spec ini. Dibanding scraping situs berita (berisiko dari sisi ToS) atau layanan berbayar, endpoint ini sudah terstruktur dan gratis untuk skala pemakaian personal/prototipe.

### 1.5 SEC Filings Umum (Non-13F)
**Sumber:** Finnhub juga punya akses ke SEC filings secara umum di free tier-nya (bukan cuma 13F) — berguna kalau nanti Risk/Red-Flag Check (`04_RISK_REDFLAG_CHECK.md`) butuh mendeteksi filing tidak biasa (misal restatement, pergantian auditor).

---

## 2. Ringkasan Sumber

| Kategori Data | Sumber Utama | Sumber Pelengkap | Status |
|---|---|---|---|
| Harga & pasar | Yahoo Finance | — | Gratis |
| Fundamental ringkas | Yahoo Finance | — | Gratis |
| Fundamental detail (10-K/10-Q) | SEC EDGAR | — | Gratis |
| Kepemilikan institusional | Finnhub (`institutional-ownership`) | SEC EDGAR 13F (raw) | Gratis (Finnhub: 60 calls/menit) |
| Berita perusahaan | Finnhub (`company-news`) | — | Gratis (free tier) |
| SEC filings umum | Finnhub | SEC EDGAR langsung | Gratis |

**Catatan rate limit:** Finnhub free tier dibatasi 60 request/menit. Untuk Screening market-wide yang memproses banyak ticker, ini perlu masuk ke strategi batching & caching yang sama seperti Yahoo Finance — lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.

---

## 3. Cara Kerja (Alur Pengumpulan)

1. Ambil daftar ticker kandidat dari hasil Screening.
2. Untuk tiap ticker: tarik harga & fundamental ringkas dari Yahoo Finance.
3. Tarik data kepemilikan institusional dari Finnhub; kalau data kosong/tidak lengkap, fallback ke SEC EDGAR 13F.
4. Tarik berita terkini dari Finnhub (`company-news`), rentang waktu yang relevan (misal 30-90 hari terakhir).
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
