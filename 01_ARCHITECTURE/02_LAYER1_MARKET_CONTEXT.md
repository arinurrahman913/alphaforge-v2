# Layer 1 — Market Context Engine

**Status:** Aktif — revisi: DAG urutan komputasi ditambahkan, kontrak paket dirujuk
**Doc version:** 1.1.0

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

Bentuk formalnya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §3 — termasuk field `kind` (direct vs derived) yang membuat Prinsip #5 ikut mengalir bersama datanya ke Layer 2, bukan cuma jadi kalimat di dokumen spec.

Contoh isi paket (ilustratif):
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

## 5. Urutan Komputasi (DAG)

Bagian "Dipakai Oleh" di spec komponen sebelumnya banyak memakai frasa "saling melengkapi" / "saling terkait". Setelah ditelusuri, frasa itu ternyata menggambarkan **kedekatan tema**, bukan aliran data — dan itu menyembunyikan fakta yang cukup penting:

**Sebelas dari dua belas komponen Layer 1 adalah leaf.** Mereka tidak butuh komponen Layer 1 lain, cuma sumber luar (Yahoo Finance, FRED, AAII, CBOE). Artinya sebelas komponen itu bisa dihitung **paralel penuh**, tanpa koordinasi.

Cuma ada **satu** dependensi mengikat di seluruh Layer 1:

```
  ┌─ business_cycle_stage ─┐
  ├─ sector_rotation ──────┤
  ├─ money_flow ───────────┤
  ├─ liquidity_conditions ─┤
  ├─ yield_curve ──────────┤   11 komponen leaf
  ├─ market_regime ────────┤   (paralel penuh)
  ├─ macro_calendar ───────┤
  ├─ currency_dxy ─────────┤
  ├─ commodity_signals ────┤
  ├─ vix ──────────────┐   │
  └─ market_breadth ───┤   │
                       │   │
                       ▼   │
              market_sentiment          ← satu-satunya composite
                       │   │
                       ▼   ▼
              Market Context Package
```

`market_sentiment` butuh `vix` + `market_breadth` (plus AAII & put/call dari luar), jadi ia **harus dihitung terakhir**.

Konsekuensi praktis: Layer 1 itu satu gelombang paralel + satu langkah akhir. Bukan rantai dua belas tahap. Ini jauh lebih murah dari yang tersirat di dokumen sebelumnya.

### Kalau Ada Komponen yang Gagal

Paket **tetap dikirim** dengan komponen itu `status=missing`. Layer 2 tidak boleh berhenti hanya karena satu dari dua belas komponen kosong (lihat `04_DATA_CONTRACTS.md` §3).

Khusus `market_sentiment`: kalau salah satu inputnya hilang, ia dihitung dari sisanya dengan `status=degraded` + catatan input mana yang hilang — bukan diam-diam berpura-pura lengkap.

### Aturan ke Depan

Kalau nanti ada komponen yang mau menjadikan komponen lain sebagai input, itu **perubahan formula** — wajib menaikkan `method_version` komponen tersebut (Prinsip #6) dan memperbarui DAG di atas. Menambah dependensi diam-diam berarti entri historis lama tidak lagi sebanding dengan yang baru.

## 6. Bagaimana Konteks Ini Dipakai di Layer 2

Market Context Package menyertai setiap saham yang dianalisa di Layer 2, tapi **tidak otomatis mengubah skor**. Masing-masing dari tiga modul reasoning (Multibagger, Quality/Compound, Speculative) punya kebebasan menafsirkan konteks ini sesuai relevansinya — misalnya modul Speculative kemungkinan besar lebih sensitif terhadap VIX dan Market Sentiment, sementara Quality/Compound lebih memperhatikan Yield Curve dan Liquidity Conditions.

Detail bagaimana tiap modul menafsirkan konteks ini didokumentasikan di spec modul masing-masing (`03_LAYER2_SPECS/07-09`).

---

© AlphaForge v2
