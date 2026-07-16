# Sector Rotation

**Status:** Aktif — revisi: kind ditambahkan (Prinsip #5)
**Doc version:** 1.1.0
**Kind:** direct

## Definisi

Pergerakan relatif dana antar sektor — sektor mana yang sedang diminati (inflow) dan sektor mana yang sedang ditinggalkan (outflow).

## Kenapa Penting

Membantu memahami apakah kekuatan/kelemahan sebuah saham didukung oleh arus sektornya secara keseluruhan, atau justru melawan arus.

## Sumber Data

Yahoo Finance — harga & volume sector ETF (XLK, XLE, XLF, XLV, XLI, dst).

## Cara Hitung / Pendekatan

Bandingkan kinerja relatif tiap sector ETF terhadap index acuan (misal S&P 500) dalam beberapa rentang waktu (1 bulan, 3 bulan) untuk melihat sektor mana yang outperform/underperform.

## Input Dari

Tidak ada komponen Layer 1 lain — dihitung dari harga sector ETF. Komponen leaf.

Business Cycle Stage memberi konteks interpretasi, tapi bukan input perhitungan.

## Digunakan Oleh

Multibagger Module (sektor yang sedang naik daun), Quality/Compound Module (relevansi sektor jangka panjang).

---

© AlphaForge v2
