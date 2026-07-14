# Knowledge

**Status:** Aktif

## Definisi

Pemahaman terstruktur tentang satu saham, dibangun dari Evidence — bukan data mentah lagi, tapi profil yang siap dipakai sebagai bahan reasoning.

## Kenapa Penting

Ini titik pemisah antara 'fakta' dan 'pemahaman'. Tanpa tahap ini eksplisit, reasoning modul akan langsung menafsirkan data mentah tanpa struktur yang konsisten.

## Sumber Data / Pendekatan

Diturunkan sepenuhnya dari Evidence — tidak menarik data baru, tidak memanggil provider apa pun secara langsung.

## Batas Paling Penting: Fakta Turunan, Bukan Penilaian

Knowledge boleh **menghitung dan mengkategorikan** dari Evidence (rasio, tren, agregasi, label berbasis ambang angka) — tapi **tidak boleh membuat penilaian kualitatif** ("bagus", "kuat", "berisiko", "menarik"). Penilaian semacam itu adalah wewenang tiga modul reasoning, bukan Knowledge.

Alasannya struktural: ketiga modul reasoning (Multibagger, Quality/Compound, Speculative) harus bereasoning independen dari Knowledge yang sama (Prinsip #3). Kalau Knowledge sudah menyelipkan kesimpulan ("pertumbuhan kuat"), ketiga modul jadi diam-diam diarahkan ke kesimpulan yang sama dari hulu — Multi-Lens-nya jadi tidak bermakna.

| Contoh | Level Knowledge (fakta turunan) | Bukan Level Knowledge (penilaian) |
|---|---|---|
| Pertumbuhan | "Revenue CAGR 3 tahun: 24%" | "Pertumbuhan kuat" |
| Margin | "Gross margin turun dari 42% ke 37% dalam 8 kuartal" | "Profitabilitas melemah" |
| Kepemilikan | "Insider selling: 3 transaksi jual, total $2.1 juta, 45 hari terakhir" | "Insider kurang percaya diri" |
| Valuasi relatif | "P/E 34, median peer group 19" | "Terlalu mahal" |

Aturan praktis: setiap field di Knowledge harus bisa dijawab tanpa kata sifat evaluatif — angka, tren, atau kategori berbasis ambang yang didefinisikan eksplisit (bukan judgment call implisit).

## Skema Knowledge Profile

Profil per ticker, lima bagian tetap (field kosong ditandai `null` + status `missing`, bukan ditebak/diinterpolasi — lihat bagian Data Hilang di bawah):

**1. Identitas & Klasifikasi** — ticker, exchange, sektor/industri, tier ukuran (dari flag Screening: micro/small/mid/large-cap), status instrumen (mis. `adr`, `recent_ipo` — diteruskan apa adanya dari flag Screening).

**2. Kesehatan Finansial** — tren revenue (YoY 4 kuartal terakhir, CAGR 3 & 5 tahun kalau data cukup), tren margin (gross/operating/net, per kuartal), struktur balance sheet (debt-to-equity, current ratio, posisi kas), tren cash flow (FCF per kuartal, FCF margin).

**3. Posisi Kompetitif (deskriptif)** — kategori model bisnis kalau bisa ditentukan dari data (mis. subscription/hardware/marketplace), pangsa revenue relatif terhadap total peer group (angka, bukan predikat "dominan/kecil" — itu tugas Peer/Relative Comparison & modul reasoning).

**4. Tren Historis** — performa harga (return 1/3/5 tahun), volatilitas historis (std dev harian, beta terhadap index), rekam jejak earnings (jumlah beat/miss dari N kuartal terakhir — angka faktual).

**5. Kepemilikan** — persentase kepemilikan institusional, persentase insider, daftar transaksi insider signifikan terbaru (tanggal, arah, jumlah — bukan interpretasi arahnya).

**Metadata** (menempel di setiap profil, bukan bagian terpisah): `evidence_snapshot_date`, daftar field yang berhasil diisi vs field yang diharapkan (jadi input langsung buat Confidence/Data Quality), daftar sumber Evidence yang dipakai.

## Data Hilang / Tidak Lengkap

Kalau Evidence tidak menyediakan data untuk suatu field (mis. data institutional kosong untuk saham yang baru IPO), field itu diisi `null` dan ditandai status `missing` — **tidak** diisi dengan estimasi, rata-rata industri, atau asumsi apa pun. Knowledge tidak menutupi kekosongan data; itu tugas Confidence/Data Quality untuk menandai dampaknya, dan modul reasoning yang memutuskan cara menyikapinya (mis. anggap netral, atau turunkan keyakinan pada bagian yang terkait).

## Cara Kerja

1. Ambil seluruh Evidence per ticker.
2. Hitung tiap field di skema di atas — murni turunan matematis/kategorisasi berbasis ambang, tanpa kata sifat evaluatif.
3. Field yang datanya tidak tersedia diisi `null` + status `missing`, bukan diinterpolasi.
4. Lampirkan metadata (tanggal snapshot, rasio kelengkapan field, daftar sumber) di setiap profil.
5. Profil yang sudah lengkap ini menjadi input seragam untuk Confidence/Data Quality, Peer/Relative Comparison, Risk/Red-Flag Check, dan ketiga modul reasoning.

## Terhubung Dengan

Confidence/Data Quality, Peer/Relative Comparison, Risk/Red-Flag Check — semua beroperasi di atas Knowledge, bukan Evidence mentah.

---

© AlphaForge v2
