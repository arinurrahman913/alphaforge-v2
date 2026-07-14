# Market Breadth

**Status:** Aktif

## Definisi

Ukuran kesehatan sebuah rally — berapa banyak saham individual yang benar-benar ikut naik/turun, bukan hanya pergerakan index secara agregat.

## Kenapa Penting

Index bisa terlihat naik padahal hanya digerakkan segelintir saham besar. Breadth mengungkap apakah kekuatan market itu luas atau sempit.

## Sumber Data

Tidak ada endpoint gratis siap pakai. Perlu daftar konstituen index (S&P 500/NASDAQ) lalu hitung sendiri dari harga tiap saham (Yahoo Finance).

## Cara Hitung / Pendekatan

Hitung advance/decline ratio dan persentase saham yang berada di atas MA200 dari seluruh konstituen index. Ini derived/approximated — butuh banyak panggilan data, bukan 1 API call.

## Dipakai Oleh

Market Regime, Market Sentiment (saling melengkapi), ketiga modul sebagai indikator kesehatan market.

---

© AlphaForge v2
