# Money Flow

**Status:** Aktif — revisi: method_version & kind ditambahkan (Prinsip #5, #6)
**Doc version:** 1.1.0
**Method version:** 1.0.0
**Kind:** derived / approximated

## Definisi

Estimasi arah aliran dana secara agregat di level sektor/market — bukan per saham individual. Sinyal setara di level saham individual ada di data kepemilikan institusional pada Evidence, Layer 2 (`03_LAYER2_SPECS/02_EVIDENCE.md`, bagian 1.3) — bukan field bernama "money flow" secara literal, karena granularitas dan sumber datanya berbeda (proxy sector ETF di sini vs. filing 13F di Evidence).

## Kenapa Penting

Menunjukkan apakah dana institusional secara umum sedang masuk atau keluar dari kelas aset/sektor tertentu, sebagai sinyal risk appetite pasar.

## Sumber Data

Data resmi granular (EPFR, Lipper) berbayar. Pendekatan gratis: proxy dari volume & price action di sector ETF (Yahoo Finance).

## Cara Hitung / Pendekatan

Gunakan kombinasi volume abnormal + pergerakan harga di sector ETF sebagai proxy arah aliran dana. Ini pendekatan derived/approximated, bukan data flow yang presisi.

## Input Dari

Tidak ada komponen Layer 1 lain — dihitung dari volume & price action sector ETF. Komponen leaf.

> Money Flow dan Sector Rotation sama-sama membaca sector ETF, tapi **tidak saling memberi input**. Versi sebelumnya menulis "saling melengkapi" — itu menggambarkan kedekatan tema, bukan aliran data.

## Digunakan Oleh

Ketiga modul reasoning sebagai bagian dari konteks makro.

---

© AlphaForge v2
