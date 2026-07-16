# Liquidity Conditions

**Status:** Aktif — revisi: kind ditambahkan (Prinsip #5)
**Doc version:** 1.1.0
**Kind:** direct

## Definisi

Kondisi likuiditas sistem keuangan secara luas — seberapa banyak 'uang' yang tersedia mengalir ke aset berisiko.

## Kenapa Penting

Salah satu driver market paling fundamental yang sering diabaikan. Likuiditas ketat biasanya menekan valuasi aset berisiko, likuiditas longgar cenderung mendukung rally.

## Sumber Data

FRED — Fed balance sheet (total assets), M2 money supply growth, credit spread (corporate bond spread).

## Cara Hitung / Pendekatan

Pantau tren Fed balance sheet dan M2 growth rate; kombinasikan dengan credit spread untuk membaca apakah kondisi sedang mengetat atau melonggar.

## Input Dari

Tidak ada komponen Layer 1 lain — dihitung dari seri FRED. Komponen leaf.

## Digunakan Oleh

Quality/Compound Module (biaya modal & valuasi jangka panjang), ketiga modul sebagai konteks makro.

---

© AlphaForge v2
