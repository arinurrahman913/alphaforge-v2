# Yield Curve

**Status:** Aktif — revisi: kind ditambahkan (Prinsip #5)
**Doc version:** 1.1.0
**Kind:** direct

## Definisi

Struktur suku bunga obligasi pemerintah AS dari berbagai tenor (misal 3-bulan, 2-tahun, 10-tahun).

## Kenapa Penting

Bentuk yield curve (normal vs inverted) secara historis jadi salah satu indikator resesi paling diawasi. Juga memengaruhi biaya modal dan valuasi relatif saham growth vs value.

## Sumber Data

FRED atau Treasury.gov — gratis, data resmi.

## Cara Hitung / Pendekatan

Ambil spread antar tenor kunci (misal 10Y-2Y) untuk menentukan apakah curve normal, flat, atau inverted.

## Input Dari

Tidak ada komponen Layer 1 lain — dihitung dari seri FRED/Treasury. Komponen leaf.

## Digunakan Oleh

Quality/Compound Module (biaya modal), ketiga modul sebagai konteks makro.

---

© AlphaForge v2
