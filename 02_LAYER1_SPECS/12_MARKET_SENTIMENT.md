# Market Sentiment

**Status:** Aktif

## Definisi

Ukuran sentimen investor secara umum — optimis (greed) atau pesimis (fear) — di luar ukuran volatilitas murni seperti VIX.

## Kenapa Penting

Sentimen ekstrem (terlalu greedy atau terlalu fearful) sering jadi indikator kontrarian yang berguna.

## Sumber Data

AAII Investor Sentiment Survey (mingguan, gratis), CBOE Put/Call Ratio (gratis, delayed). Tidak ada API resmi gratis untuk versi 'Fear & Greed Index' ala CNN.

## Cara Hitung / Pendekatan

Kombinasikan AAII survey + put/call ratio + VIX + market breadth menjadi satu pembacaan sentimen sendiri. Ini derived/approximated, bukan versi identik dari index komersial manapun.

## Dipakai Oleh

Volatility Index (saling melengkapi), Speculative Module (paling relevan untuk sentimen ekstrem sebagai sinyal kontrarian).

---

© AlphaForge v2
