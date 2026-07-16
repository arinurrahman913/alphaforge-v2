# Peer / Relative Comparison

**Status:** Aktif — revisi: posisi di pipeline diperjelas (barrier Fase A/B), ambang minimum peer group, penanganan peer gagal, kontrak output (D-03)
**Doc version:** 2.0.0

## Definisi

Perbandingan suatu saham dengan kompetitor atau industri sejenis, bukan dievaluasi murni berdiri sendiri.

## Kenapa Penting

Sebuah angka (misal margin, ROE, P/E) sulit dinilai 'bagus' atau 'jelek' tanpa konteks pembanding — bagus secara absolut belum tentu bagus relatif terhadap industrinya.

Sejak D-03, komponen ini juga jadi **satu-satunya tempat angka relatif-peer hidup**. Knowledge sengaja dibuat murni per-ticker; semua yang butuh membandingkan antar ticker ada di sini.

## Posisi di Pipeline: Kenapa Ada Barrier

Ini bagian yang paling sering disalahpahami dari diagram lama.

Diagram sebelumnya menggambar `Knowledge → [Confidence + Peer] → Risk` seolah alur lurus per ticker. Itu tidak mungkin: **Peer butuh Knowledge saham lain**. Selama Knowledge ticker sesektor belum jadi, Peer untuk ticker ini tidak bisa dihitung. Jadi pipeline Layer 2 sebenarnya dua fase, bukan satu:

```
Fase A  (per-ticker, bisa paralel penuh)
  Evidence → Knowledge

  ═══════════ barrier ═══════════
  tunggu Knowledge SELURUH kandidat selesai

Fase B  (butuh populasi)
  Peer → Confidence → Risk → 3 modul → Aggregator
```

Barrier ini bukan pilihan desain — ia konsekuensi tak terhindarkan dari memakai populasi hasil screening sendiri sebagai sumber peer. Menggambarnya eksplisit lebih baik daripada menemukannya saat implementasi.

### Konsekuensi yang harus disadari

- **Peer tidak bisa jalan streaming.** Analisa satu ticker tunggal (mis. lewat CLI `analyze AAPL`) tidak punya populasi. Untuk kasus itu, `peer_tickers` disuplai manual oleh caller, dan `peer_group_basis` ditandai `manual` — bukan `screening_universe`. Bedanya harus terlihat di output, karena kualitas peer group-nya beda jauh.
- **Memori.** Fase A menghasilkan Knowledge untuk ~1.500–3.000 kandidat yang semuanya harus ada sebelum Fase B mulai. Ini pertama kalinya pipeline butuh menahan populasi utuh di satu titik — perlu masuk hitungan saat implementasi.

## Sumber Data / Pendekatan

Knowledge dari saham-saham lain di sektor/industri yang sama (hasil dari proses Screening & Evidence yang sama). Tidak menarik data baru, tidak memanggil provider — sama seperti Knowledge, Peer murni turunan.

## Cara Kerja

1. Kelompokkan kandidat berdasarkan klasifikasi sektor/industri (bagian 1 Knowledge).
2. Untuk tiap ticker target, ambil peer group-nya.
3. Bandingkan metrik kunci terhadap **median** grup (bukan rata-rata — median tahan terhadap outlier, dan peer group saham hampir selalu punya outlier).
4. Hasilkan percentile posisi ticker di grupnya.
5. Tandai kalau grup terlalu kecil (lihat di bawah).

### Ambang Minimum Peer Group

| Ukuran grup | Perlakuan |
|---|---|
| ≥ 5 | Normal |
| 3 – 4 | `low_sample_size` — hasil tetap dipakai, Confidence bagian relatif diturunkan, modul yang memakainya wajib menyebut |
| < 3 | Perbandingan **tidak dihitung**. Metrik diisi `null` + status `missing`, bukan dipaksakan |

Median dari 2 saham bukan median — itu rata-rata dua angka yang kebetulan bertetangga. Memaksakan angka di situ menghasilkan sesuatu yang terlihat presisi tapi tidak berarti apa-apa, dan itu persis jenis kepalsuan yang Prinsip #5 ingin cegah.

> Ambang ini konsisten dengan implementasi yang sudah ada (`peer_comparison.py` menandai `low_sample_size` saat peer group < 3). Yang berubah di spec: batas < 3 sekarang **tidak menghitung sama sekali**, bukan menghitung sambil menandai.

### Peer yang Gagal Ditarik

Peer yang datanya gagal diambil dicatat eksplisit di `peer_failures`, **tidak di-skip diam-diam**. Alasannya: peer group yang menyusut dari 8 jadi 3 karena kegagalan data menghasilkan median yang beda jauh dari peer group yang memang cuma 3 — dan tanpa catatan, dua kondisi itu tidak bisa dibedakan di kemudian hari.

## Output

Bentuk lengkapnya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §5 (`PeerComparisonResult`).

## Yang Masih Perlu Diputuskan

- **Sumber klasifikasi sektor/industri.** Yahoo Finance punya sektor & industri, tapi granularitasnya kasar dan kadang aneh (perusahaan yang sama bisa dikategorikan beda dari GICS). Perlu dites: apakah grup yang dihasilkan masuk akal, atau butuh penyesuaian manual per sektor.
- **Metrik mana saja yang dibandingkan.** Kandidat: margin (gross/operating/net), ROE, revenue growth, rasio valuasi (bagian 6 Knowledge), debt-to-equity. Daftar final menunggu kriteria tiga modul selesai — percuma menghitung relatif untuk metrik yang tidak ada modul yang memakainya.
- **Peer discovery otomatis.** Saat ini `peer_tickers` masih disuplai manual oleh caller. Dengan Fase B beroperasi di atas populasi screening, ini semestinya bisa otomatis — tapi biaya komputasinya belum dihitung.

## Terhubung Dengan

Knowledge (input, seluruh populasi Fase A), Confidence/Data Quality (menerima `low_sample_size` & `peer_failures` sebagai penurun skor bagian relatif), Quality/Compound Module (konsumen utama — paling relevan untuk menilai keunggulan kompetitif relatif), Multibagger & Speculative (konsumen sekunder, terutama untuk valuasi relatif).

---

© AlphaForge v2
