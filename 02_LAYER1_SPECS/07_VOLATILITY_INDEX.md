# Volatility Index (VIX)

**Status:** Aktif — revisi: kind ditambahkan (Prinsip #5)
**Doc version:** 1.1.0
**Kind:** direct

## Definisi

Indikator standar untuk mengukur ekspektasi volatilitas pasar (risk appetite) berdasarkan harga opsi S&P 500.

## Kenapa Penting

VIX rendah biasanya menandakan kondisi complacent/risk-on; VIX tinggi menandakan fear/risk-off. Salah satu sinyal makro paling banyak dipakai.

## Sumber Data

Yahoo Finance (`^VIX`) — gratis, langsung tersedia.

## Cara Hitung / Pendekatan

Ambil nilai VIX terkini dan bandingkan dengan rata-rata historisnya untuk menentukan level relatif (rendah/normal/tinggi).

## Input Dari

Tidak ada — satu angka langsung dari Yahoo Finance (`^VIX`). Komponen leaf, `kind=direct`.

## Digunakan Oleh

**Market Sentiment** (input komputasi — salah satu dari empat), Speculative Module (paling sensitif terhadap risk appetite).

---

© AlphaForge v2
