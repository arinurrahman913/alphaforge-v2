# Screening

**Status:** Aktif

## Definisi

Tahap awal Layer 2 yang menyaring seluruh market (NASDAQ + NYSE) menjadi kandidat saham yang layak diproses lebih dalam ke tahap Evidence.

## Kenapa Penting

Memproses ribuan ticker sekaligus ke tahap Evidence yang berat itu mahal dan lambat. Screening jadi saringan murah di awal supaya proses berikutnya lebih efisien.

## Sumber Data / Pendekatan

Daftar ticker: NASDAQ listing files (`nasdaqlisted.txt`, `otherlisted.txt`) — gratis, publik. Data harga/fundamental dasar: Yahoo Finance.

## Cara Kerja

Filter dipecah jadi dua tingkat: **Hard Exclude** (gagal, tidak lanjut ke Evidence sama sekali) dan **Soft Flag** (lolos, tapi ditandai untuk dipertimbangkan modul reasoning & Confidence Score di tahap berikutnya). Pemisahan ini penting karena ketiga modul reasoning (Multibagger, Quality/Compound, Speculative) butuh populasi saham yang berbeda-beda — filter yang terlalu ketat di awal bisa membuang kandidat valid untuk Multibagger/Speculative (yang justru sering nano/small-cap).

### Hard Exclude — saham ini tidak diproses lebih lanjut

| Kriteria | Ambang | Alasan |
|---|---|---|
| Tipe listing | Bukan saham biasa (common stock) — exclude ETF, warrant, right, unit SPAC pra-merger, preferred share | Bukan objek analisa "perusahaan", model reasoning tidak berlaku |
| Status test issue | Ditandai "Test Issue" di listing file | Bukan saham riil |
| Market cap | < $30 juta | Di bawah ini biasanya shell company atau data terlalu tipis untuk dianalisa secara bertanggung jawab |
| Rata-rata dollar volume harian (20 hari) | < $300,000 | Likuiditas terlalu tipis — harga gampang gap/manipulasi, data tidak representatif |
| Harga saham | < $0.50 | Rawan manipulasi & data noise ekstrem (sering sudah kena beberapa reverse split) |
| Ketersediaan data fundamental | Tidak ada laporan keuangan kuartalan dalam 2 kuartal terakhir | Tidak ada bahan baku untuk membangun Evidence |
| Riwayat harga | < 20 hari data harga tersedia | Terlalu baru untuk dihitung indikator dasar apa pun |

### Soft Flag — lolos, tapi ditandai untuk tahap berikutnya

| Kriteria | Ambang | Ditandai sebagai |
|---|---|---|
| Market cap | $30 juta – $300 juta | `micro_cap` — Confidence Score default lebih rendah, relevan untuk Multibagger & Speculative |
| Market cap | $300 juta – $2 miliar | `small_cap` |
| Riwayat harga | 20 – 252 hari (< 1 tahun) | `recent_ipo` — beberapa rasio historis (mis. tren 5 tahun) tidak bisa dihitung, tidak menggugurkan saham |
| Jenis instrumen | ADR (American Depositary Receipt) | `adr` — standar akuntansi bisa beda (non-GAAP), Knowledge harus catat ini eksplisit |
| Rata-rata dollar volume harian | $300,000 – $1 juta | `low_liquidity` — bukan exclude, tapi Speculative Module perlu tahu slippage/risk eksekusi lebih tinggi |
| Kelengkapan data institutional (13F) | Tidak ada data kepemilikan institusional | `no_institutional_data` — bukan gagal total, tapi menurunkan Confidence di bagian itu |

### Kenapa Ambangnya Segini (Ringkas)

- Ambang market cap & volume sengaja **rendah** (bukan mis. $2 miliar) supaya universe Multibagger dan Speculative — yang secara alami banyak berisi nano/small-cap — tidak keburu terbuang di Screening. Yang dibuang cuma yang benar-benar tidak bisa dianalisa (shell, data kosong, tidak likuid sama sekali).
- Filter "keras" dibatasi ke hal yang membuat analisa **secara struktural tidak mungkin** (data kosong, bukan saham biasa). Hal yang membuat saham "berisiko" tapi tetap bisa dianalisa (micro-cap, ADR, IPO baru) sengaja **tidak** di-exclude — itu tanggung jawab Confidence Score dan modul reasoning masing-masing, bukan Screening.

### Cara Kerja — Sumber Data per Tahap Filter

1. **Hard exclude tipe listing & test issue** — langsung dari kolom di `nasdaqlisted.txt` / `otherlisted.txt`, tanpa panggilan API sama sekali.
2. **Market cap, harga, riwayat data** — dari data fundamental yang **sudah di-cache** (refresh berkala, bukan live call per ticker saat screening jalan — lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`).
3. **Volume rata-rata 20 hari** — dihitung dari data harga historis yang juga sudah di-cache.
4. Hanya kandidat yang lolos hard exclude yang kemudian ditarik data live/terbarunya di tahap Evidence.

### Estimasi Corong (Funnel)

Ilustratif, perlu divalidasi saat implementasi: dari ±6.000–8.000 ticker NASDAQ+NYSE gabungan, hard exclude di atas diperkirakan menyisakan sekitar 1.500–3.000 kandidat yang lanjut ke Evidence. Angka pasti tergantung kondisi market saat itu — dicatat di log Screening setiap kali dijalankan, bukan diasumsikan tetap.

### Yang Masih Perlu Divalidasi Saat Implementasi

- Apakah ambang $30 juta market cap dan $300,000 volume harian perlu disesuaikan setelah lihat hasil funnel riil (kalau ternyata cuma menyisakan 200 ticker atau malah 5.000, ambang perlu dikalibrasi ulang).
- Apakah exclude ADR perlu jadi hard exclude untuk versi pertama (supaya Knowledge tidak perlu menangani standar akuntansi ganda dulu), lalu dibuka lagi di iterasi berikutnya.

## Terhubung Dengan

Evidence (menerima output kandidat dari sini). Perlu strategi rate-limit & caching — lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.

---

© AlphaForge v2
