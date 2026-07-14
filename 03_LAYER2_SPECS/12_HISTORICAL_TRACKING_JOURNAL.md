# Historical Tracking / Decision Journal

**Status:** Aktif

## Definisi

Mekanisme penyimpanan hasil analisa dari waktu ke waktu, untuk divalidasi terhadap apa yang benar-benar terjadi pada saham tersebut di kemudian hari.

## Kenapa Penting

Tanpa ini, tidak ada cara mengukur secara empiris apakah reasoning v2 benar-benar lebih akurat dari v1 — atau hanya terlihat lebih meyakinkan karena strukturnya lebih rapi.

## Sumber Data / Pendekatan

Menyimpan snapshot Output (hasil ketiga modul + confidence + flag) beserta tanggal analisa.

## Cara Kerja

Simpan setiap hasil analisa sebagai entri historis. Secara periodik, bandingkan entri lama dengan pergerakan harga/kejadian aktual saham tersebut untuk mengevaluasi akurasi reasoning dari waktu ke waktu.

## Terhubung Dengan

Menerima input dari Aggregator & Output. Fase implementasi bisa menyusul setelah alur inti (Screening→Output) berjalan — dicatat sebagai kandidat v2.1 kalau perlu diprioritaskan belakangan.

---

© AlphaForge v2
