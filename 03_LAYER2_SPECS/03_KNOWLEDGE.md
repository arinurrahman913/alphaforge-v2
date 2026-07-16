# Knowledge

**Status:** Aktif — revisi: bagian 6 (Valuasi) & 7 (Governance & Peristiwa Filing) ditambahkan, field relatif-peer dikeluarkan (D-01,D-02,D-03); bagian 3 dipecah 3a/3b (D-13)
**Doc version:** 3.0.0

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
| Valuasi | "P/E 34, P/S 8.2, FCF yield 1.8%" | "Terlalu mahal" |

Aturan praktis: setiap field di Knowledge harus bisa dijawab tanpa kata sifat evaluatif — angka, tren, atau kategori berbasis ambang yang didefinisikan eksplisit (bukan judgment call implisit).

## Skema Knowledge Profile

Profil per ticker, lima bagian tetap (field kosong ditandai `null` + status `missing`, bukan ditebak/diinterpolasi — lihat bagian Data Hilang di bawah):

**1. Identitas & Klasifikasi** — ticker, exchange, sektor/industri, tier ukuran (dari flag Screening: micro/small/mid/large-cap), status instrumen (mis. `adr`, `recent_ipo` — diteruskan apa adanya dari flag Screening).

**2. Kesehatan Finansial** — tren revenue (YoY 4 kuartal terakhir, CAGR 3 & 5 tahun kalau data cukup), tren margin (gross/operating/net, per kuartal), struktur balance sheet (debt-to-equity, current ratio, posisi kas), tren cash flow (FCF per kuartal, FCF margin).

**3a. Posisi Kompetitif — Struktur (D-13)** — kategori model bisnis kalau bisa ditentukan dari data (mis. subscription/hardware/marketplace), konsentrasi revenue per segmen bisnis (dari 10-K kalau tersedia), skala absolut (revenue TTM, jumlah karyawan kalau ada), estimasi ukuran TAM kalau tersedia dari filing/riset pihak ketiga yang dikutip perusahaan.

**3b. Posisi Kompetitif — Momentum (D-13)** — pertumbuhan tiap segmen bisnis (persen YoY/QoQ), tren guidance vs konsensus analis (naik/turun/sesuai, dari rilis berturut), sinyal akselerasi atau deselerasi (percepatan pertumbuhan dibanding kuartal sebelumnya).

> **Kenapa bagian 3 dipecah (D-13):** 3a menjawab "seberapa besar ruangnya", 3b menjawab "apakah sedang bergerak ke arah situ". Quality/Compound cuma boleh membaca 3a — mesin yang awet tidak perlu sedang berakselerasi untuk dianggap awet, dan kalau Quality boleh membaca momentum, ia perlahan tergoda mengandalkannya dan menempel ke Multibagger (lihat D-12). Multibagger membaca keduanya, karena trajectory butuh tahu ruangnya (3a) dan geraknya (3b) sekaligus.
>
> **Catatan (D-03):** field "pangsa revenue relatif terhadap total peer group" **dikeluarkan** dari 3a. Field itu membuat Knowledge butuh pengetahuan tentang ticker lain, padahal Peer/Relative Comparison justru beroperasi *di atas* Knowledge — melingkar. Semua angka relatif-peer hidup di output Peer (`06_PEER_RELATIVE_COMPARISON.md`).

**4. Tren Historis** — performa harga (return 1/3/5 tahun), volatilitas historis (std dev harian, beta terhadap index), rekam jejak earnings (jumlah beat/miss dari N kuartal terakhir — angka faktual).

**5. Kepemilikan** — persentase kepemilikan institusional, persentase insider, daftar transaksi insider signifikan terbaru (tanggal, arah, jumlah — bukan interpretasi arahnya).

**6. Valuasi** — rasio valuasi absolut per ticker: P/E (trailing & forward kalau tersedia), P/S, EV/EBITDA, P/B, FCF yield. Semua **angka absolut saja** — perbandingan terhadap peer bukan di sini, tapi di `06_PEER_RELATIVE_COMPARISON.md` (D-02, D-03).

Rasio yang secara matematis tidak bermakna diisi `null` + status `missing`, bukan angka negatif yang menyesatkan — mis. P/E untuk perusahaan yang rugi, EV/EBITDA saat EBITDA negatif. Ini kasus `missing` yang **wajar dan informatif**, bukan kegagalan data: Confidence tidak boleh menghukumnya sekeras data yang hilang karena provider gagal, karena keduanya beda arti. Modul reasoning yang memutuskan artinya (Speculative mungkin tidak peduli; Quality/Compound mungkin sangat peduli).

**7. Governance & Peristiwa Filing** — bahan baku Risk/Red-Flag Check, diturunkan dari Evidence 1.5 (SEC filings) & 1.4 (berita):

- Tren shares outstanding (perubahan % dalam 12 bulan terakhir — bahan deteksi dilusi)
- Riwayat pergantian auditor (tanggal, dari-ke, sumber filing)
- Restatement laporan keuangan (tanggal, periode yang direstate)
- Litigasi material yang tercatat di filing (jenis, status, tanggal)
- Filing tidak biasa lainnya (mis. 8-K item 4.01, late filing/NT 10-K)

Semua tetap tunduk aturan yang sama: fakta dan tanggal, **tanpa kata sifat evaluatif**. "Auditor berganti 2 kali dalam 3 tahun" itu Knowledge; "governance lemah" itu bukan — itu wewenang Risk/Red-Flag Check dan modul reasoning.

> **Kenapa bagian ini ada (D-01):** sebelum revisi ini, Risk/Red-Flag Check menyebut sumbernya "Knowledge & Evidence" — padahal dokumen ini menutup dengan aturan bahwa Risk beroperasi di atas Knowledge, bukan Evidence mentah. Dua-duanya tidak bisa benar. Akarnya: Risk butuh field yang memang tidak ada di skema ini, jadi ia terpaksa menjangkau Evidence. Bagian 7 menutup lubang itu supaya invariant satu arah tetap utuh — dan sebagai bonus, Confidence jadi bisa mengukur kelengkapan data governance, yang mustahil selama field-nya cuma hidup di Evidence.

## Akses Field Per Modul (D-12)

`KnowledgeProfile` yang sama disusun sekali, tapi tiap modul reasoning cuma boleh membaca subset-nya. Ini bukan detail implementasi — ini yang membuat tiga modul benar-benar jadi tiga lensa, bukan satu lensa dengan tiga suara (lihat D-12 di `00_Foundation/04_DECISIONS.md` untuk pembelaan tiap baris).

| Bagian | Multibagger | Quality/Compound | Speculative |
|---|:---:|:---:|:---:|
| 1 — Identitas | ✅ | ✅ | ✅ |
| 2 — Kesehatan finansial | arah saja | ✅ penuh | ❌ |
| 3a — Struktur | ✅ | ✅ | ❌ |
| 3b — Momentum | ✅ | ❌ | ❌ |
| 4 — Tren historis | ✅ | ✅ penuh | volatilitas saja |
| 5 — Kepemilikan | ❌ | ✅ | ✅ |
| 6 — Valuasi | ✅ | ✅ | ❌ |
| 7 — Governance | ❌ | ✅ | ❌ |

Pelanggaran batas ini — field di luar akses modul muncul di `context_used` atau mempengaruhi `stance` — adalah bug, bukan pilihan implementasi.

**Metadata** (menempel di setiap profil, bukan bagian terpisah): `evidence_snapshot_date`, `method_version`, daftar field yang berhasil diisi vs field yang diharapkan (jadi input langsung buat Confidence/Data Quality), daftar sumber Evidence yang dipakai.

## Data Hilang / Tidak Lengkap

Kalau Evidence tidak menyediakan data untuk suatu field (mis. data institutional kosong untuk saham yang baru IPO), field itu diisi `null` dan ditandai status `missing` — **tidak** diisi dengan estimasi, rata-rata industri, atau asumsi apa pun. Knowledge tidak menutupi kekosongan data; itu tugas Confidence/Data Quality untuk menandai dampaknya, dan modul reasoning yang memutuskan cara menyikapinya (mis. anggap netral, atau turunkan keyakinan pada bagian yang terkait).

## Invariant: Knowledge Murni Per-Ticker

**`KnowledgeProfile` suatu ticker harus bisa dihitung dengan hanya Evidence ticker itu di tangan.** Tidak ada field yang membutuhkan pengetahuan tentang ticker lain, tentang sektornya secara agregat, atau tentang hasil screening keseluruhan.

Ini bukan preferensi gaya — ini yang membuat pipeline Fase A bisa jalan paralel per ticker tanpa koordinasi, dan yang memutus lingkaran Knowledge↔Peer (D-03). Gampang dites: jalankan Knowledge dengan Evidence satu ticker saja. Kalau gagal, atau ada field yang harusnya terisi malah kosong, invariant-nya sudah bocor.

Satu-satunya "pengetahuan luar" yang boleh masuk adalah `screening_flags` — dan itu diteruskan apa adanya dari Screening tentang ticker ini sendiri, bukan hasil perbandingan dengan ticker lain.

## Cara Kerja

1. Ambil seluruh Evidence per ticker.
2. Hitung tiap field di skema di atas — murni turunan matematis/kategorisasi berbasis ambang, tanpa kata sifat evaluatif.
3. Field yang datanya tidak tersedia diisi `null` + status `missing`, bukan diinterpolasi.
4. Lampirkan metadata (tanggal snapshot, `method_version`, rasio kelengkapan field, daftar sumber) di setiap profil.
5. Profil yang sudah lengkap ini menjadi input seragam untuk Confidence/Data Quality, Peer/Relative Comparison, Risk/Red-Flag Check, dan ketiga modul reasoning.

## Terhubung Dengan

Confidence/Data Quality, Peer/Relative Comparison, Risk/Red-Flag Check — semua beroperasi di atas Knowledge, bukan Evidence mentah. Sejak bagian 7 ada (D-01), aturan ini berlaku **tanpa pengecualian**: Risk tidak lagi perlu menjangkau Evidence.

Bentuk paketnya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §4.

---

© AlphaForge v2
