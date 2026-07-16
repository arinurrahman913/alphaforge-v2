# Confidence / Data Quality Score

**Status:** Aktif — revisi untuk kelengkapan skema & konsistensi dengan Prinsip #5 (`00_Foundation/02_PRINCIPLES.md`)

## Definisi

Ukuran seberapa kuat dan lengkap evidence yang mendasari sebuah kesimpulan tentang saham individual. Cakupannya per Prinsip #5: menempel di Knowledge, di tiap hasil tiga modul reasoning, dan di Output — bukan cuma di satu titik.

## Kenapa Penting

Tanpa ini, kesimpulan dari data yang tipis/parsial (misal data institutional belum lengkap) akan terlihat sama meyakinkannya dengan kesimpulan dari data yang solid — padahal seharusnya tidak.

## Komponen Skor (Ilustratif — Perlu Dikalibrasi Saat Implementasi)

Confidence dihitung dari kombinasi beberapa dimensi, bukan satu angka tunggal dari satu sumber:

1. **Kelengkapan field** — rasio field Knowledge yang terisi vs. total field yang diharapkan (dari metadata `missing` yang sudah didefinisikan di `03_KNOWLEDGE.md`).
2. **Kebaruan data** — seberapa baru `evidence_snapshot_date` dibanding tanggal analisa berjalan; data fundamental yang berumur lebih dari 1 kuartal penuh menurunkan skor.
3. **Konsistensi antar-sumber** — apakah data dari sumber berbeda (misal Yahoo Finance vs. SEC EDGAR untuk fundamental) saling menguatkan atau berbeda signifikan.
4. **Flag dari Screening** — status seperti `micro_cap`, `recent_ipo`, `no_institutional_data` (lihat `01_SCREENING.md`) secara default menurunkan confidence pada bagian data yang terkait, bukan keseluruhan skor.

## Format Output (Perlu Diputuskan Sebelum Implementasi)

Belum diputuskan apakah output-nya berupa:
- Skor numerik (misal 0–100), atau
- Kategori diskrit (`low` / `medium` / `high`), atau
- Keduanya — skor numerik untuk pemrosesan internal, kategori untuk ditampilkan ke pengguna.

Detail bobot tiap komponen di atas dan ambang kategori didiskusikan lebih lanjut mendekati fase implementasi — dicatat di sini sebagai keputusan terbuka, bukan diasumsikan.

## Cakupan di Luar Layer 2

Komponen Layer 1 yang derived/approximated (Business Cycle Stage, Money Flow, Market Sentiment) tunduk pada semangat prinsip yang sama (Prinsip #5) tapi **tidak** memakai skema skor formal di dokumen ini — mekanismenya cukup pernyataan eksplisit di level definisi komponen bahwa pembacaannya dikonstruksi/didekati (sudah ada di tiap spec Layer 1 terkait).

## Kontrak & Hubungan dengan Confidence Modul (D-11)

Menghasilkan `ConfidenceReport` — bentuknya di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §5c. Berjalan di **Fase B** karena butuh `peer_failures` & `low_sample_size` dari Peer.

Sampai revisi ini komponen ini punya spec tapi **tidak punya output yang didefinisikan** — yang ada cuma `confidence` inline di `ModuleOutput`, sehingga tidak jelas #5 ini komponen atau bukan.

Dua hal yang selama ini kabur, sekarang tegas:

| | Mengukur | Jumlah |
|---|---|---|
| `ConfidenceReport` | seberapa kuat **data** saham ini | 1 per ticker |
| `ModuleOutput.confidence` | seberapa yakin **modul ini** pada kesimpulannya | 3 per ticker |

**Aturan V6 — `ModuleOutput.confidence.score` ≤ `ConfidenceReport.overall.score`.** Modul tidak boleh lebih yakin pada kesimpulannya daripada data yang menopangnya. Tanpa V6, modul bisa mengaku yakin 90 di atas profil yang 60% lengkap dan tidak ada yang menghentikannya.

Kebalikannya tetap sah dan penting: modul **boleh** jauh kurang yakin dari data yang tersedia. Data lengkap tidak mewajibkan kesimpulan kuat — sering justru sebaliknya.

## Terhubung Dengan

Knowledge (sumber perhitungan), Screening (flag awal yang jadi input), tiga modul reasoning (menerima & meneruskan confidence ke kesimpulannya masing-masing), Aggregator & Output (ditampilkan bersama hasil akhir).

---

© AlphaForge v2
