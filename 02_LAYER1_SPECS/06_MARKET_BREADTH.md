# Market Breadth

**Status:** Aktif — revisi: universe diputuskan (D-05), method_version ditambahkan
**Doc version:** 2.0.0
**Method version:** 1.0.0
**Kind:** derived / approximated

## Definisi

Ukuran kesehatan sebuah rally — berapa banyak saham individual yang benar-benar ikut naik/turun, bukan hanya pergerakan index secara agregat.

## Kenapa Penting

Index bisa terlihat naik padahal hanya digerakkan segelintir saham besar. Breadth mengungkap apakah kekuatan market itu luas atau sempit.

## Universe: Hasil Screening Sendiri, Bukan S&P 500 (D-05)

Versi sebelumnya menyebut "perlu daftar konstituen index (S&P 500/NASDAQ)", dan itu diperlakukan seolah tinggal disuplai. Ternyata bukan begitu masalahnya: **daftar konstituen S&P 500 resmi itu berlisensi** (S&P Dow Jones Indices). Yang gratis cuma scraping Wikipedia atau menebak dari holdings ETF. Jadi ini bukan input yang belum diberikan — sumber gratisnya memang tidak ada.

**Keputusan:** breadth dihitung atas **universe hasil Screening sendiri** — seluruh NASDAQ + NYSE dari listing files, setelah hard exclude (`03_LAYER2_SPECS/01_SCREENING.md`).

Alasannya bukan cuma karena gratis. Seluruh alasan Layer 2 menyaring market-wide adalah karena watchlist manual v1 membatasi apa yang bisa terlihat. Mengukur "kesehatan market" lewat 500 saham terbesar, lalu menganalisa saham di luar 500 itu, berarti konteksnya diukur dari populasi yang berbeda dari yang dianalisa. Memakai universe sendiri justru lebih konsisten dengan v2, bukan sekadar kompromi.

Ambang hard exclude Screening (market cap > $30jt, dollar volume > $300rb, harga > $0.50) sudah membuang shell company dan ticker mati — persis noise yang bikin breadth mentah tidak bermakna.

### Konsekuensi yang Diterima Secara Sadar

Angkanya **tidak sebanding** dengan pembacaan breadth publik manapun ($ADD, "% saham di atas MA200" versi S&P). Breadth atas ~2.000 saham hasil screening akan sistematis berbeda dari breadth atas 500 mega-cap — terutama saat rally sempit, yang justru saat breadth paling berguna.

Ini bukan bug, tapi **wajib dinyatakan di output** supaya tidak keliru dibaca sebagai indikator standar. Field `kind=derived` + `note` di `ComponentReading` (`01_ARCHITECTURE/04_DATA_CONTRACTS.md` §3) menampung ini.

## Sumber Data

Listing files NASDAQ (`nasdaqlisted.txt`, `otherlisted.txt`) untuk universe; Yahoo Finance untuk harga tiap saham. Keduanya gratis, keduanya sudah dibutuhkan Screening — jadi tidak ada dependensi sumber baru.

## Cara Hitung / Pendekatan

Atas universe hasil hard exclude Screening:
1. **Advance/decline ratio** — jumlah saham naik vs turun pada sesi terakhir.
2. **Persentase di atas MA200** — porsi saham yang harganya di atas moving average 200 harinya.

Derived/approximated — butuh banyak panggilan data, bukan 1 API call.

> **Catatan biaya:** ini komponen Layer 1 paling mahal, dan satu-satunya yang butuh data harga seluruh universe. Ia memakai data harga historis yang **sudah di-cache** untuk Screening (`04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`) — bukan tarikan terpisah. Kalau cache Screening belum terisi, breadth `status=missing`, bukan memicu ribuan call sendiri.

## Input Dari

Tidak ada komponen Layer 1 lain. Breadth adalah komponen leaf — ia cuma butuh harga & universe.

## Digunakan Oleh

Market Sentiment (sebagai salah satu dari empat input), ketiga modul Layer 2 sebagai indikator kesehatan market.

---

© AlphaForge v2
