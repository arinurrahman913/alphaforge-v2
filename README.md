# AlphaForge v2

Pembangunan ulang inti reasoning AlphaForge dari nol.

- **Layer 1 — Market Context Engine**: membaca kondisi makro & market secara keseluruhan (12 komponen) sebelum saham manapun dianalisa.
- **Layer 2 — Stock Analysis Engine**: Screening (market-wide) → Evidence → Knowledge → Peer → Confidence → Risk/Red-Flag Check → tiga modul reasoning independen (Multibagger, Quality/Compound, Speculative) → Aggregator (tampilkan ketiganya, bukan satu verdict) → Historical Tracking.

v1 (`alphaforge-v1` / `alphaforge-core-v1`) tetap hidup sebagai referensi — tidak dihapus, tidak digabung.

## Struktur Dokumen

```
00_Foundation/       — Charter, Principles, Glossary, Decision Log
01_ARCHITECTURE/     — System Overview, spesifikasi Layer 1 & 2, Kontrak Data
02_LAYER1_SPECS/     — 12 spec komponen Market Context Engine
03_LAYER2_SPECS/     — 12 spec komponen Stock Analysis Engine
04_DATA_SOURCES/     — Provider data, strategi rate-limit & caching
```

## Status

**Spec:** 37 dokumen. Fondasi, arsitektur, dan Layer 1 lengkap. Di Layer 2, wadahnya sudah dikunci (kontrak data, aturan validasi) — **isi kriteria & bobot tiga modul reasoning belum**, dan itu keputusan terbuka terbesar yang tersisa.

**Kode:** ada di repo terpisah `alphaforge-core-v2`, mengikuti pola v1 (`alphaforge` untuk dokumentasi, `alphaforge-core` untuk kode). Implementasi awal Layer 1 (12/12) dan Layer 2 end-to-end sudah jalan dan tervalidasi dengan data live. Beberapa hal diketahui belum lengkap — lihat `PROGRESS_2026-07-14.md`.

> **Catatan:** revisi spec terbaru (lihat `CHANGELOG.md`) mengubah beberapa hal yang **kode-nya belum ikut menyesuaikan** — terutama kontrak output tiga modul (D-04), bagian 6 & 7 skema Knowledge (D-01, D-02), barrier Fase A/B (D-03), dan universe Market Breadth (D-05). Spec sekarang mendahului kode di titik-titik ini, bukan sebaliknya.

## Mulai Baca Dari Mana

1. `00_Foundation/01_CHARTER.md` — kenapa v2 dibuat, apa bedanya dari v1
2. `00_Foundation/04_DECISIONS.md` — keputusan arsitektur yang sudah diambil + alternatif yang ditolak
3. `01_ARCHITECTURE/01_SYSTEM_OVERVIEW.md` — gambaran alur end-to-end
4. `01_ARCHITECTURE/04_DATA_CONTRACTS.md` — bentuk tiap paket data yang mengalir
5. Lanjut ke spec detail sesuai layer yang ingin didalami

## Konvensi Dokumen

- **`Doc version`** (semver) di header tiap spec — versi dokumennya.
- **`Method version`** (semver) — versi *formula*-nya, wajib untuk komponen derived/approximated. Ini yang dicatat di tiap entri Historical Tracking (Prinsip #6). Perubahan formula yang mengubah hasil wajib menaikkannya.
- **`Kind`** — `direct` (satu angka dari satu sumber otoritatif) vs `derived` (dikonstruksi dari kombinasi sumber). Ikut mengalir ke Layer 2 lewat `ComponentReading.kind` supaya Prinsip #5 tidak berhenti sebagai kalimat di dokumen.
- Keputusan desain dicatat di `00_Foundation/04_DECISIONS.md` sebagai `D-xx`, lengkap dengan alternatif yang ditolak dan biaya kalau dibalik. Entri tidak dihapus — kalau dibatalkan, statusnya diubah.

---

© AlphaForge v2
