# Market Listing Sources

**Status:** Aktif

## Kebutuhan

Sebelum Screening (Layer 2) bisa jalan, sistem butuh **daftar seluruh ticker** yang eksis di NASDAQ dan NYSE. Yahoo Finance tidak menyediakan endpoint ini, jadi perlu sumber terpisah.

## Sumber

- **NASDAQ Listing Files** (`nasdaqlisted.txt`, `otherlisted.txt`) — file listing publik yang bisa diunduh langsung, mencakup NASDAQ dan ticker non-NASDAQ (termasuk NYSE) yang terdaftar di sistem yang sama. Gratis, jadi sumber paling umum dipakai untuk kebutuhan ini.
- **SEC EDGAR** — punya company list resmi juga, dan sudah ada provider-nya (`sec_edgar.py`) di kode v1. Bisa jadi sumber pelengkap/verifikasi silang.

## Cara Pakai

1. Unduh/tarik listing file secara berkala (tidak perlu real-time — daftar ticker tidak berubah drastis harian).
2. Simpan sebagai cache lokal untuk dipakai ulang oleh proses Screening, supaya tidak perlu re-fetch tiap kali scan dijalankan.
3. Gabungkan dengan Evidence dari Yahoo Finance untuk mendapat detail tiap ticker.

## Catatan

Daftar mentah ini akan berisi ribuan ticker, termasuk yang tidak likuid/tidak relevan (micro-cap sangat kecil, ticker yang baru delisting, dll). Filter awal Screening (market cap, volume minimum) bertugas mempersempit ini sebelum masuk ke Evidence yang lebih berat.

---

© AlphaForge v2
