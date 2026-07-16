# Historical Tracking / Decision Journal

**Status:** Aktif — revisi: pemisahan penyimpanan (v2.0) vs evaluasi (v2.1), pencatatan versi metodologi
**Doc version:** 2.0.0

## Definisi

Mekanisme penyimpanan hasil analisa dari waktu ke waktu, untuk divalidasi terhadap apa yang benar-benar terjadi pada saham tersebut di kemudian hari.

## Kenapa Penting

Tanpa ini, tidak ada cara mengukur secara empiris apakah reasoning v2 benar-benar lebih akurat dari v1 — atau hanya terlihat lebih meyakinkan karena strukturnya lebih rapi.

## Penyimpanan vs Evaluasi — Dipisah Sejak Revisi Ini

Prinsip #6 mengizinkan komponen ini menyusul di v2.1. **Yang boleh ditunda cuma evaluasinya, bukan penyimpanannya.**

| | Kapan | Kenapa |
|---|---|---|
| **Penyimpanan** snapshot | **v2.0 — sejak hari pertama** | Murah (tulis JSON per analisa). Kalau ditunda, saat v2.1 datang tidak ada apa-apa untuk dievaluasi |
| **Evaluasi** terhadap kenyataan | v2.1 | Butuh waktu berlalu dulu supaya ada "kenyataan" untuk dibandingkan. Ini yang wajar ditunda |

Ini koreksi terhadap cara pengecualian v2.1 dibaca sebelumnya. Menunda seluruh komponen berarti jam mulai berjalan baru saat v2.1 rilis — dan alat pembuktian satu-satunya yang dipunyai v2 baru menghasilkan data pertamanya berbulan-bulan setelah klaim "v2 lebih baik dari v1" dibuat. Menyimpan sejak awal membuat jam mulai berjalan hari ini, dengan biaya mendekati nol.

Konsekuensi praktis: `HistoricalEntry.outcome` sengaja `null` saat entri dibuat. Field-nya sudah ada di v2.0, isinya menyusul di v2.1.

## Sumber Data / Pendekatan

Menyimpan snapshot `AggregatorOutput` utuh (hasil ketiga modul + confidence + flag) beserta tanggal analisa. **Snapshot utuh, bukan ringkasan** — ringkasan yang dibuat hari ini mengandaikan kita sudah tahu apa yang penting untuk dievaluasi nanti, dan itu justru yang belum diketahui.

Bentuk lengkapnya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §8 (`HistoricalEntry`).

## Versi Metodologi (Prinsip #6)

Tiap entri wajib mencatat `method_versions` — peta komponen → semver yang berlaku saat kesimpulan itu dibuat. Disalin ke level entri, bukan cuma tertanam di dalam snapshot, supaya audit bisa memfilter per versi tanpa membongkar isi tiap snapshot.

Tanpa ini, audit di masa depan berisiko membandingkan kesimpulan lama dengan formula yang sudah berubah tanpa disadari — terutama untuk komponen derived/approximated (Business Cycle Stage, Money Flow, Market Sentiment, Market Breadth) yang logikanya memang diharapkan berubah seiring iterasi.

## Cara Kerja

1. **v2.0** — setiap `AggregatorOutput` yang keluar disimpan sebagai `HistoricalEntry`, termasuk yang `halted=true`. `outcome` dibiarkan `null`.
2. **v2.1** — secara periodik, bandingkan entri lama dengan pergerakan harga/kejadian aktual saham tersebut, isi `outcome`, evaluasi akurasi reasoning per modul dan per versi metodologi.

## Yang Masih Perlu Diputuskan

- **Bentuk `outcome`.** Return absolut? Relatif terhadap index? Horizon berapa lama (dan berbeda per modul — Speculative jelas beda horizon dari Quality/Compound)? Ini pertanyaan v2.1, tapi bentuk penyimpanannya sebaiknya tidak menutup opsi.
- **Media penyimpanan** — file JSON per entri, SQLite, atau lainnya. Terkait dengan pertanyaan cache di `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.

## Terhubung Dengan

Menerima input dari Aggregator & Output.

---

© AlphaForge v2
