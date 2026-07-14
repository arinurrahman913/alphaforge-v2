# AlphaForge v2

Pembangunan ulang inti reasoning AlphaForge dari nol.

- **Layer 1 — Market Context Engine**: membaca kondisi makro & market secara keseluruhan (12 komponen) sebelum saham manapun dianalisa.
- **Layer 2 — Stock Analysis Engine**: Screening (market-wide) → Evidence → Knowledge → Risk/Red-Flag Check → tiga modul reasoning independen (Multibagger, Quality/Compound, Speculative) → Aggregator (tampilkan ketiganya, bukan satu verdict) → Historical Tracking.

v1 (`alphaforge-v1` / `alphaforge-core-v1`) tetap hidup sebagai referensi — tidak dihapus, tidak digabung.

## Struktur Dokumen

```
00_Foundation/       — Charter, Principles, Glossary
01_ARCHITECTURE/     — System Overview, spesifikasi arsitektur Layer 1 & 2
02_LAYER1_SPECS/     — 12 spec komponen Market Context Engine
03_LAYER2_SPECS/     — 12 spec komponen Stock Analysis Engine
04_DATA_SOURCES/     — Provider data, strategi rate-limit & caching
```

## Status

Struktur & spec konten dasar sudah lengkap (32 dokumen). Implementasi kode belum dimulai — akan menyusul di repo terpisah, mengikuti pola v1 (`alphaforge` untuk dokumentasi, `alphaforge-core` untuk kode).

## Mulai Baca Dari Mana

1. `00_Foundation/01_CHARTER.md` — kenapa v2 dibuat, apa bedanya dari v1
2. `01_ARCHITECTURE/01_SYSTEM_OVERVIEW.md` — gambaran alur end-to-end
3. Lanjut ke spec detail sesuai layer yang ingin didalami

---

© AlphaForge v2
