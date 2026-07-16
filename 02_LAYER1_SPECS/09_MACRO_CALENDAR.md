# Macro Economic Calendar

**Status:** Aktif — revisi: kind ditambahkan (Prinsip #5)
**Doc version:** 1.1.0
**Kind:** direct

## Definisi

Jadwal rilis data ekonomi penting yang biasa menggerakkan market — CPI (inflasi), keputusan suku bunga The Fed (FOMC), laporan tenaga kerja (NFP).

## Kenapa Penting

Volatilitas market sering melonjak di sekitar tanggal rilis data ini. Mengetahui jadwalnya membantu memahami konteks pergerakan harga jangka pendek.

## Sumber Data

FRED release calendar (gratis, resmi). Versi 'user-friendly' seperti di Investing.com/Forex Factory biasanya lewat scraping — berisiko dari sisi ToS, sebaiknya dihindari atau dipakai hati-hati.

## Cara Hitung / Pendekatan

Tarik jadwal rilis data utama dari FRED, tandai tanggal-tanggal dengan potensi dampak tinggi.

## Input Dari

Tidak ada — ditarik langsung dari FRED release calendar. Komponen leaf.

## Digunakan Oleh

Semua modul Layer 2, sebagai penanda 'apakah saat ini mendekati periode volatil'.

---

© AlphaForge v2
