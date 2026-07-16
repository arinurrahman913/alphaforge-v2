# Catalyst Tracking

**Status:** Aktif — revisi: kontrak `CatalystSet`, posisi di Fase A, kenapa bukan bagian Knowledge (D-11)
**Doc version:** 2.0.0
**Method version:** 1.0.0

## Definisi

Pelacakan peristiwa spesifik yang diperkirakan menggerakkan harga saham — tanggal earnings, peluncuran produk, persetujuan regulasi, dan sejenisnya.

## Kenapa Penting

Tanpa ini, modul Speculative bisa menandai saham sebagai 'menarik' tapi tidak actionable — investor tidak tahu 'menunggu apa' dan 'sampai kapan'.

## Sumber Data / Pendekatan

Kalender earnings (Yahoo Finance), berita & filing terkait (SEC EDGAR, sumber berita).

## Cara Kerja

Identifikasi peristiwa mendatang yang relevan untuk saham tertentu, beri estimasi tanggal dan potensi dampaknya.

## Posisi & Kontrak (D-11)

Berjalan di **Fase A**, per-ticker, diturunkan dari Evidence. Menghasilkan `CatalystSet` — bentuknya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §5b.

Sampai revisi ini komponen ini **yatim**: ada di daftar file, ada di diagram, disebut Speculative — tapi tidak punya kontrak, tidak ada di `AggregatorOutput`, dan tidak punya slot di Knowledge. Artinya modul Speculative merujuk katalis yang tidak punya field di mana pun.

### Kenapa bukan bagian Knowledge

Katalis bukan fakta tentang keadaan perusahaan — ia fakta tentang kalender. Alasan pokoknya **umur simpan**: `KnowledgeProfile` adalah potret pada satu `evidence_snapshot_date` dan seluruh isinya meluruh dengan laju yang sama. Katalis punya kedaluwarsa masing-masing — earnings 19 Juli mati pada 20 Juli, margin 42% tidak.

Satu paket berisi field dengan laju luruh berbeda akan salah dibaca cepat atau lambat. Karena itu tiap katalis wajib punya `expires_at` eksplisit, supaya dashboard bisa menandai yang sudah lewat alih-alih menampilkannya seolah masih ditunggu.

### Kepastian Bertingkat

`certainty: scheduled | expected | rumored` — earnings terjadwal dan rumor akuisisi tidak boleh masuk sebagai jenis benda yang sama. Modul Speculative berhak tahu bedanya, dan `rumored` sepatutnya menurunkan confidence, bukan menaikkan stance.

## Terhubung Dengan

Dipakai terutama oleh Speculative Module sebagai bagian dari reasoning-nya.

---

© AlphaForge v2
