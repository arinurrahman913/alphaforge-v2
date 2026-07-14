# Layer 1 — Market Context Engine

**Status:** Aktif

---

## 1. Tujuan

Layer 1 menjawab satu pertanyaan besar sebelum saham manapun disentuh: **"Bagaimana kondisi lingkungan investasi saat ini?"**

Tanpa layer ini, setiap saham dinilai seolah berdiri sendiri di ruang hampa — padahal kinerja dan risiko sebuah saham selalu dipengaruhi kondisi market, sektor, dan makro di sekitarnya.

## 2. Dua Belas Komponen

Layer 1 tersusun dari 12 komponen, dikelompokkan menjadi 4 tema:

**Siklus & Struktur Ekonomi**
1. Business Cycle Stage
2. Sector Rotation

**Aliran Uang & Likuiditas**
3. Money Flow
4. Liquidity Conditions
5. Yield Curve

**Kesehatan & Sentimen Market**
6. Market Breadth
7. Volatility Index (VIX)
8. Market Regime
9. Market Sentiment

**Konteks Eksternal**
10. Macro Economic Calendar
11. Currency / Dollar Index (DXY)
12. Commodity Signals

Spec detail tiap komponen (definisi, sumber data, cara hitung) ada di `02_LAYER1_SPECS/`.

## 3. Output: Market Context Package

Layer 1 tidak menghasilkan skor tunggal seperti "market sedang bagus/jelek". Ia menghasilkan **paket konteks** — kumpulan pembacaan dari 12 komponen di atas, yang kemudian dipakai Layer 2 sebagai referensi, bukan sebagai filter otomatis.

Contoh isi paket (ilustratif, bukan format final):
```
{
  "business_cycle_stage": "mid-cycle",
  "market_regime": "bull, above MA200",
  "vix": "low (risk-on)",
  "yield_curve": "normal, not inverted",
  "sector_rotation": "rotating into industrials, out of utilities",
  ...
}
```

## 4. Kapan Dihitung

Market Context Package dihitung **sekali per sesi analisa**, bukan berulang per saham. Semua saham yang dianalisa dalam satu sesi screening/analyze memakai paket konteks yang sama.

## 5. Bagaimana Konteks Ini Dipakai di Layer 2

Market Context Package menyertai setiap saham yang dianalisa di Layer 2, tapi **tidak otomatis mengubah skor**. Masing-masing dari tiga modul reasoning (Multibagger, Quality/Compound, Speculative) punya kebebasan menafsirkan konteks ini sesuai relevansinya — misalnya modul Speculative kemungkinan besar lebih sensitif terhadap VIX dan Market Sentiment, sementara Quality/Compound lebih memperhatikan Yield Curve dan Liquidity Conditions.

Detail bagaimana tiap modul menafsirkan konteks ini didokumentasikan di spec modul masing-masing (`03_LAYER2_SPECS/07-09`).

---

© AlphaForge v2
