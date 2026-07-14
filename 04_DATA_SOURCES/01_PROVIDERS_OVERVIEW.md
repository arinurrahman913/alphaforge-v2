# Providers Overview

**Status:** Aktif

---

## 1. Prinsip Sumber Data

Sesuai Prinsip #7 (`00_Foundation/02_PRINCIPLES.md`): **gratis dulu, berbayar belakangan**. Dari audit terhadap 12 komponen Layer 1 dan kebutuhan Layer 2, tidak ada satupun yang *mengharuskan* data berbayar — tapi sebagian butuh kerja ekstra untuk dikonstruksi dari kombinasi sumber gratis.

## 2. Klasifikasi Sumber Data

**Direct API (tinggal panggil, gratis):**
- Yield Curve, VIX, DXY, Commodity Signals, Liquidity Conditions, Business Cycle Stage, Sector Rotation, Market Regime — semua lewat Yahoo Finance atau FRED.

**Derived / Approximated (dikonstruksi dari kombinasi data gratis, butuh effort ekstra):**
- Market Breadth — perlu daftar konstituen index + hitung manual dari harga tiap saham.
- Macro Calendar (versi user-friendly) — versi resmi (FRED release calendar) gratis, versi "cantik" ala Investing.com berisiko scraping.
- Money Flow (level saham/sektor granular) — data resmi (EPFR dll) berbayar, memakai proxy volume+price action di sector ETF sebagai gantinya.
- Market Sentiment (Fear & Greed lengkap) — tidak ada API resmi gratis, dikonstruksi dari kombinasi AAII survey + put/call ratio + VIX + breadth.

## 3. Daftar Provider

| Provider | Dipakai Untuk | Status |
|---|---|---|
| Yahoo Finance | Harga, fundamental, VIX, DXY, commodity futures, sector ETF | Gratis, sudah dipakai di v1 |
| FRED | Yield curve, GDP, PMI, unemployment, M2, Fed balance sheet | Gratis, resmi |
| SEC EDGAR | Filing resmi, 13F institutional holdings | Gratis, resmi, sudah ada provider-nya di v1 |
| NASDAQ Listing Files | Daftar ticker NASDAQ + NYSE | Gratis, publik |
| AAII Investor Sentiment | Survey sentimen mingguan | Gratis |
| CBOE Put/Call Ratio | Rasio put/call | Gratis, delayed |

## 4. Referensi Detail

- `02_YAHOO_FINANCE.md` — cakupan & limitasi
- `03_MARKET_LISTING_SOURCES.md` — sumber daftar ticker
- `04_MACRO_DATA_SOURCES.md` — sumber data makro (FRED, dll)
- `05_RATE_LIMIT_CACHING_STRATEGY.md` — strategi menghindari rate-limit saat screening market-wide

---

© AlphaForge v2
