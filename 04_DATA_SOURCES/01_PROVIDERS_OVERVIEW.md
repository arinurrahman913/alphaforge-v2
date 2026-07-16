# Providers Overview

**Status:** Aktif — revisi: standar scraping dinyatakan eksplisit, universe breadth (D-05)
**Doc version:** 2.0.0

---

## 1. Prinsip Sumber Data

Sesuai Prinsip #7 (`00_Foundation/02_PRINCIPLES.md`): **gratis dulu, berbayar belakangan**. Dari audit terhadap 12 komponen Layer 1 dan kebutuhan Layer 2, tidak ada satupun yang *mengharuskan* data berbayar — tapi sebagian butuh kerja ekstra untuk dikonstruksi dari kombinasi sumber gratis.

> **Catatan asumsi free tier:** "gratis" harus diverifikasi per endpoint, bukan per provider. Contoh konkret: endpoint Finnhub `institutional-ownership` awalnya diasumsikan gratis tapi terbukti premium-only (HTTP 403) saat testing. Karena itu jalur gratis yang dijamin untuk detail kepemilikan institusional adalah SEC EDGAR 13F (raw, parse sendiri), dengan persentase agregat dari Yahoo Finance — lihat `03_LAYER2_SPECS/02_EVIDENCE.md`. Klaim "tidak ada yang mengharuskan berbayar" tetap berlaku justru karena fallback gratis ini ada.

## 2. Klasifikasi Sumber Data

**Direct API (tinggal panggil, gratis):**
- Yield Curve, VIX, DXY, Commodity Signals, Liquidity Conditions, Business Cycle Stage, Sector Rotation, Market Regime — semua lewat Yahoo Finance atau FRED.

**Derived / Approximated (dikonstruksi dari kombinasi data gratis, butuh effort ekstra):**
- Market Breadth — dihitung atas **universe hasil Screening sendiri** (NASDAQ+NYSE dari listing files), bukan konstituen S&P 500. Daftar konstituen S&P 500 resmi berlisensi; yang gratis cuma scraping Wikipedia atau menebak dari holdings ETF. Lihat D-05 di `00_Foundation/04_DECISIONS.md` dan `02_LAYER1_SPECS/06_MARKET_BREADTH.md`.
- Macro Calendar (versi user-friendly) — versi resmi (FRED release calendar) gratis, versi "cantik" ala Investing.com berisiko scraping.
- Money Flow (level saham/sektor granular) — data resmi (EPFR dll) berbayar, memakai proxy volume+price action di sector ETF sebagai gantinya.
- Market Sentiment (Fear & Greed lengkap) — tidak ada API resmi gratis, dikonstruksi dari kombinasi AAII survey + put/call ratio + VIX + breadth.

## 2b. Standar Scraping — Kenapa yfinance Boleh tapi Investing.com Tidak

Ini perlu dinyatakan eksplisit karena dokumen-dokumen sebelumnya terlihat memakai dua standar berbeda tanpa alasan tertulis: `09_MACRO_CALENDAR.md` menolak scraping Investing.com/Forex Factory karena risiko ToS, tapi seluruh sistem berdiri di atas `yfinance` yang `02_YAHOO_FINANCE.md` sendiri akui berbasis scraping.

Perbedaannya nyata, tapi bukan "yang satu scraping, yang satu tidak":

| | `yfinance` | Investing.com / Forex Factory |
|---|---|---|
| Ada alternatif resmi gratis? | Tidak untuk cakupan ini | **Ya** — FRED release calendar |
| Sifat data | Harga publik, di banyak sumber lain | Kalender, tersedia resmi dari sumber aslinya |
| Yang di-scrape | Library pihak ketiga yang mapan | Halaman situs langsung |

**Aturannya:** kalau ada sumber resmi gratis yang menyediakan data yang sama, pakai itu — scraping tidak boleh jadi jalan pintas untuk tampilan yang lebih enak. Kalau tidak ada alternatif resmi dan datanya publik, scraping lewat library mapan diterima sebagai risiko sadar.

Konsekuensinya konsisten: **AAII Investor Sentiment** (dipakai Market Sentiment) tidak punya API resmi — praktisnya scraping juga. Ia lolos aturan di atas karena tidak ada alternatif resmi, tapi statusnya harus dicatat jujur di tabel, bukan disebut "gratis" begitu saja seolah setara dengan FRED.

Risiko yang diterima: `yfinance` bisa rusak sewaktu-waktu tanpa pemberitahuan (`02_YAHOO_FINANCE.md`). Itu risiko tunggal terbesar di seluruh sistem — hampir semua data harga lewat sana. Belum ada rencana fallback; dicatat sebagai keputusan terbuka.

## 3. Daftar Provider

| Provider | Dipakai Untuk | Status |
|---|---|---|
| Yahoo Finance | Harga, fundamental, VIX, DXY, commodity futures, sector ETF | Gratis, sudah dipakai di v1. **Bukan API resmi** (scraping) — titik kegagalan tunggal terbesar, lihat §2b |
| FRED | Yield curve, GDP, PMI, unemployment, M2, Fed balance sheet | Gratis, resmi |
| SEC EDGAR | Filing resmi, 13F institutional holdings, berita/8-K | Gratis, resmi, sudah ada provider-nya di v1 |
| NASDAQ Listing Files | Daftar ticker NASDAQ + NYSE; juga universe untuk Market Breadth (D-05) | Gratis, publik |
| AAII Investor Sentiment | Survey sentimen mingguan | Gratis, **tanpa API resmi** — praktisnya scraping, lihat §2b |
| CBOE Put/Call Ratio | Rasio put/call | Gratis, delayed |
| Finnhub | Berita perusahaan (`company-news`), SEC filings umum | Free tier **per endpoint** — `institutional-ownership` premium-only (403), tidak dipakai; endpoint lain wajib diverifikasi sebelum diandalkan |

## 4. Referensi Detail

- `02_YAHOO_FINANCE.md` — cakupan & limitasi
- `03_MARKET_LISTING_SOURCES.md` — sumber daftar ticker
- `04_MACRO_DATA_SOURCES.md` — sumber data makro (FRED, dll)
- `05_RATE_LIMIT_CACHING_STRATEGY.md` — strategi menghindari rate-limit saat screening market-wide

---

© AlphaForge v2
