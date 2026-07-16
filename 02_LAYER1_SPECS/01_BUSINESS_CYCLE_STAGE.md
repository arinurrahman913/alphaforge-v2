# Business Cycle Stage

**Status:** Aktif — revisi: method_version & kind ditambahkan (Prinsip #5, #6)
**Doc version:** 1.1.0
**Method version:** 1.0.0
**Kind:** derived / approximated

## Definisi

Posisi ekonomi secara fundamental dalam siklus bisnis: early-cycle, mid-cycle, late-cycle, atau recession.

## Kenapa Penting

Setiap fase siklus punya karakter sektor yang berbeda-beda (early-cycle cenderung consumer discretionary & tech unggul, late-cycle cenderung energy & materials unggul). Komponen ini jadi fondasi konseptual untuk membaca Sector Rotation.

## Sumber Data

FRED (Federal Reserve Economic Data) — indikator seperti GDP growth, ISM PMI, tingkat pengangguran. Semua gratis.

## Cara Hitung / Pendekatan

Kombinasikan beberapa indikator makro (PMI, GDP QoQ, unemployment trend) menjadi satu klasifikasi fase siklus. Bukan satu angka tunggal dari satu sumber — perlu logika gabungan sendiri (derived/approximated).

## Input Dari

Tidak ada komponen Layer 1 lain — dihitung langsung dari indikator FRED. Komponen leaf.

> Yield Curve dan Liquidity Conditions **bukan** input komputasi ke sini, meski sama-sama membaca kondisi makro. Versi sebelumnya menulis keduanya "saling terkait", yang kabur — kalau nanti keduanya benar-benar mau dijadikan input, itu perubahan formula dan wajib menaikkan `method_version`.

## Digunakan Oleh

Ketiga modul reasoning Layer 2 sebagai bagian dari Market Context Package. Sector Rotation memakainya sebagai **konteks interpretasi** (fase siklus menentukan sektor mana yang biasanya unggul), bukan sebagai input perhitungan — Sector Rotation tetap dihitung murni dari kinerja relatif ETF.

---

© AlphaForge v2
