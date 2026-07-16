# Market Sentiment

**Status:** Aktif — revisi: method_version & kind ditambahkan (Prinsip #5, #6)
**Doc version:** 1.1.0
**Method version:** 1.0.0
**Kind:** derived / approximated

## Definisi

Ukuran sentimen investor secara umum — optimis (greed) atau pesimis (fear) — di luar ukuran volatilitas murni seperti VIX.

## Kenapa Penting

Sentimen ekstrem (terlalu greedy atau terlalu fearful) sering jadi indikator kontrarian yang berguna.

## Sumber Data

AAII Investor Sentiment Survey (mingguan, gratis), CBOE Put/Call Ratio (gratis, delayed). Tidak ada API resmi gratis untuk versi 'Fear & Greed Index' ala CNN.

## Cara Hitung / Pendekatan

Kombinasikan AAII survey + put/call ratio + VIX + market breadth menjadi satu pembacaan sentimen sendiri. Ini derived/approximated, bukan versi identik dari index komersial manapun.

## Input Dari

**Satu-satunya komponen Layer 1 yang punya input dari komponen Layer 1 lain:**

- **Volatility Index (VIX)** — wajib
- **Market Breadth** — wajib

Plus sumber luar: AAII survey, CBOE put/call ratio.

Karena itu Market Sentiment **harus dihitung setelah** VIX dan Market Breadth selesai. Ini satu-satunya urutan yang mengikat di dalam Layer 1 — lihat `01_ARCHITECTURE/02_LAYER1_MARKET_CONTEXT.md` §5.

Kalau VIX atau Breadth `status=missing`, Market Sentiment dihitung dari input yang tersisa dengan `status=degraded` + `note` yang menyebut input mana yang hilang — bukan `missing` total, dan bukan diam-diam berpura-pura lengkap.

## Digunakan Oleh

Speculative Module (paling relevan — sentimen ekstrem sebagai sinyal kontrarian), ketiga modul sebagai konteks risk appetite.

---

© AlphaForge v2
