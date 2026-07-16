# Gambaran Sistem (System Overview)

**Status:** Aktif — revisi: barrier Fase A/B, rujukan kontrak data
**Doc version:** 2.1.0

---

## 1. Diagram Alur

```
                    LAYER 1 — MARKET CONTEXT ENGINE
    ┌───────────────────────────────────────────────────────────┐
    │  Business Cycle Stage · Sector Rotation · Money Flow        │
    │  Liquidity Conditions · Yield Curve · Market Breadth         │
    │  Volatility (VIX) · Market Regime · Macro Calendar           │
    │  Currency (DXY) · Commodity Signals · Market Sentiment       │
    └───────────────────────────────────────────────────────────┘
                                │
                                ▼  (Market Context Package)
                    LAYER 2 — STOCK ANALYSIS ENGINE
    ┌───────────────────────────────────────────────────────────┐
    │  FASE A — per-ticker, paralel                                │
    │  Screening (NASDAQ + NYSE, market-wide)                      │
    │           ↓                                                 │
    │  Evidence (fakta terverifikasi per ticker)                   │
    │           ↓                                                 │
    │  Knowledge (dibangun DARI evidence, murni per-ticker)         │
    │  ═══════════════ BARRIER ═══════════════                     │
    │  FASE B — butuh populasi                                     │
    │  Peer/Relative Comparison · Confidence/Data Quality           │
    │           ↓                                                 │
    │  Risk / Red-Flag Check (gerbang)                              │
    │           ↓                                                 │
    │  ┌────────────┬──────────────────┬─────────────────┐        │
    │  │ Multibagger │ Quality/Compound │  Speculative      │       │
    │  │             │                  │  + Catalyst       │       │
    │  │             │                  │    Tracking       │       │
    │  └────────────┴──────────────────┴─────────────────┘        │
    │           ↓             ↓                 ↓                 │
    │                    Aggregator                                │
    │        (tampilkan 3 sudut pandang, bukan 1 verdict)           │
    │           ↓                                                 │
    │                     Output                                   │
    │           ↓                                                 │
    │      Historical Tracking / Decision Journal                   │
    └───────────────────────────────────────────────────────────┘
```

## 2. Prinsip Desain Utama

- **Satu arah aliran data.** Setiap tahap hanya menerima output dari tahap sebelumnya lewat "paket" yang jelas bentuknya — tidak ada tahap yang memanggil balik ke belakang. Bentuk tiap paket dikunci di `04_DATA_CONTRACTS.md`; sebelum dokumen itu ada, klaim ini tidak bisa dicek.
- **Dua fase, bukan satu alur lurus.** Fase A (Evidence→Knowledge) jalan per-ticker paralel; Fase B (Peer→…→Aggregator) butuh populasi utuh. Barrier di antaranya nyata dan tidak bisa dihindari — lihat `03_LAYER2_STOCK_ANALYSIS.md` §2.4.
- **Layer 1 dihitung sekali, dipakai berkali-kali.** Market Context Package tidak dihitung ulang per saham; ia dihitung untuk kondisi market saat itu dan dipakai sebagai input bersama untuk semua saham yang diproses di sesi yang sama.
- **Tiga modul reasoning berjalan independen.** Mereka menerima Knowledge dan Market Context yang sama, tapi masing-masing punya logika reasoning sendiri dan tidak saling mempengaruhi satu sama lain.
- **Aggregator tidak menyimpulkan.** Tugasnya menyusun tampilan, bukan memutuskan mana dari tiga pandangan yang "benar".

## 3. Batas Tanggung Jawab Tiap Bagian

| Bagian | Tanggung jawab | Bukan tanggung jawabnya |
|---|---|---|
| Layer 1 | Membaca kondisi market/makro | Menganalisa saham individual |
| Screening | Mempersempit universe saham | Menilai kualitas saham |
| Evidence | Mengumpulkan & memverifikasi fakta | Menafsirkan fakta |
| Knowledge | Menyusun pemahaman terstruktur | Membuat kesimpulan investasi |
| Risk/Red-Flag | Menyaring masalah serius | Menilai potensi upside |
| 3 Modul Reasoning | Menilai dari lensa masing-masing | Memaksakan satu jawaban final |
| Aggregator | Menyusun tampilan gabungan | Memilih "pemenang" antar modul |
| Historical Tracking | Menyimpan & membandingkan hasil | Mengubah kesimpulan yang sudah dikeluarkan |

## 4. Referensi

- Detail Layer 1 → `02_LAYER1_MARKET_CONTEXT.md`
- Detail Layer 2 → `03_LAYER2_STOCK_ANALYSIS.md`
- Spec tiap komponen → folder `02_LAYER1_SPECS/` dan `03_LAYER2_SPECS/`
- Bentuk tiap paket data → `04_DATA_CONTRACTS.md`
- Lapisan tampilan → `05_DASHBOARD_LOCAL.md`
- Keputusan arsitektur & alternatif yang ditolak → `00_Foundation/04_DECISIONS.md`

---

© AlphaForge v2
