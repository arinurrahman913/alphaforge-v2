# Rate Limit & Caching Strategy

**Status:** Aktif — perlu didetailkan lebih lanjut sebelum implementasi

## Kenapa Ini Krusial

v1 hanya menarik data untuk ~33 ticker watchlist manual. v2 melakukan Screening ke **seluruh NASDAQ + NYSE** — ribuan ticker. Karena `yfinance` bukan API resmi (berbasis scraping), memanggilnya secara masif dan cepat berisiko:

- Kena rate-limit sementara (request ditolak/lambat)
- Berpotensi diblokir sementara oleh Yahoo kalau pola request terlihat mencurigakan

## Prinsip Strategi

1. **Screening dulu, Evidence belakangan.** Filter awal Screening harus memakai data seminim mungkin (idealnya dari cache/listing file, bukan panggilan live ke Yahoo Finance untuk setiap ticker), supaya cuma kandidat yang lolos filter yang ditarik detail lengkapnya.

2. **Caching wajib, bukan opsional.** Data yang tidak berubah tiap menit (fundamental, listing, sektor) disimpan di cache lokal dengan masa berlaku (TTL) yang sesuai — tidak perlu ditarik ulang tiap kali scan dijalankan.

3. **Batching & delay antar request.** Saat menarik data untuk banyak ticker, request dipecah jadi batch kecil dengan jeda antar batch, bukan dikirim semua sekaligus.

4. **Retry dengan backoff.** Kalau ada request yang gagal (kena limit), sistem mencoba ulang dengan jeda yang makin panjang, bukan langsung dianggap gagal permanen.

5. **Market Context (Layer 1) dihitung sekali per sesi**, bukan per ticker — ini otomatis mengurangi jumlah panggilan berulang secara signifikan.

## Yang Masih Perlu Diputuskan

- Berapa lama TTL cache yang wajar untuk tiap jenis data (harga real-time vs fundamental kuartalan vs listing ticker)?
- Apakah caching disimpan lokal (file/SQLite) atau butuh solusi lain?
- Berapa ukuran batch dan jeda yang aman secara empiris untuk `yfinance`?

Detail ini didiskusikan lebih lanjut saat mendekati fase implementasi Screening.

---

© AlphaForge v2
