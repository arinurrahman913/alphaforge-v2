# Screening

**Status:** Aktif

## Definisi

Tahap awal Layer 2 yang menyaring seluruh market (NASDAQ + NYSE) menjadi kandidat saham yang layak diproses lebih dalam ke tahap Evidence.

## Kenapa Penting

Memproses ribuan ticker sekaligus ke tahap Evidence yang berat itu mahal dan lambat. Screening jadi saringan murah di awal supaya proses berikutnya lebih efisien.

## Sumber Data / Pendekatan

Daftar ticker: NASDAQ listing files (`nasdaqlisted.txt`, `otherlisted.txt`) — gratis, publik. Data harga/fundamental dasar: Yahoo Finance.

## Cara Kerja

Filter kasar berdasarkan kriteria murah dihitung: market cap minimum, volume rata-rata minimum, ketersediaan data fundamental. Kriteria detail (angka pastinya) didiskusikan terpisah sebelum implementasi.

## Terhubung Dengan

Evidence (menerima output kandidat dari sini). Perlu strategi rate-limit & caching — lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.

---

© AlphaForge v2
