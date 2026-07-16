# Kriteria Tiga Modul — Draft Komitmen v1

**Status:** Draft kerja, bukan spec resmi — belum masuk repo
**Tanggal:** 16 Juli 2026
**Basis:** jawaban 4-pasangan + kasus INTC/PG/MSFT dari tes sebelumnya

---

> **⚠ KOREKSI 16 Juli (setelah dokumen ini ditulis):** tabel kriteria Quality di bawah **sudah digantikan** versi yang membedakan reinvestasi vs erosi — lihat `03_LAYER2_SPECS/08_MODULE_QUALITY_COMPOUND.md` untuk tabel final dan `00_Foundation/04_DECISIONS.md` D-13 untuk kronologi koreksinya. Verdict MSFT di dokumen ini (`compounding_rapuh`) sudah tidak berlaku — direvisi jadi `compounding_kuat`. Dibiarkan apa adanya sebagai jejak draft pertama.

---

## 0. Ini Sekarang Punya Siapa

Kamu bilang "percayakan ke saya sebagai arsitek." Saya kerjakan — tapi harus jujur soal apa artinya itu.

Sampai titik ini saya nolak nulis kriteria karena itu tesis investasimu, bukan tugas spec. Sekarang kamu delegasikan eksplisit, dua kali. Saya hormatin itu — tapi hasilnya adalah **kriteria ini sekarang punya dua penulis**, dan kamu perlu baca sampai habis, bukan cuma acc. Kalau ada yang kerasa salah pas dibaca, itu sinyal paling berharga yang bisa kamu kasih — jangan ditelan cuma karena "katanya arsitek".

Ini bukan jawaban final. Ini draft yang siap distress-test persis kayak D-08 saya stress-test pakai INTC kemarin dan ketahuan salah.

---

## 1. Fork Gap vs Momentum — Diselesaikan Lewat Opsi Ketiga

Saya tanya kamu pilih Gap (ada yang belum diketahui pasar) atau Momentum (arus udah mulai kelihatan). Jawabanmu nunjuk ke Momentum — *"mulai dilirik investor"*, *"AI sekarang trend"*.

Tapi Momentum murni ada masalah: itu nyaris sama persis sama pertanyaan Speculative ("ada asimetri berkatalis?"). Kalau Multibagger cuma "ikutin arus yang lagi kuat", dia jadi Speculative dengan nama beda.

**Yang sebenarnya kamu tunjuk, saya pikir, adalah opsi ketiga: TRAJECTORY.**

> Multibagger bukan taruhan bahwa pasar belum tahu (Gap), dan bukan taruhan pada satu peristiwa (Momentum/katalis). Dia taruhan bahwa **kurva pertumbuhan yang udah kelihatan sekarang, kalau diteruskan 3-5 tahun, menghasilkan kelipatan dari basis sekarang — dan harga sekarang cuma nge-price tahun depan, bukan lima tahun ke depan.**

Cek ke jawabanmu: PLTR "mulai dilirik investor" bukan satu event yang selesai/nggak selesai — itu **cerita ekspansi multi-tahun** (dari kontrak pemerintah ke komersial, adopsi AIP). TSM "AI trend" bukan satu katalis — itu **siklus pembangunan kapasitas bertahun-tahun**. Keduanya trajectory, bukan event tunggal.

Ini juga yang membedakan dari Speculative secara bersih: **Speculative butuh tanggal resolusi. Multibagger nggak butuh tanggal — dia butuh kurva yang terus, dengan atau tanpa satu peristiwa tertentu.**

Dan ini yang bikin kasus INTC saya kemarin (external foundry $174M) sebenarnya **lebih lemah** dari PLTR/TSM versi kamu — $174M bukan trajectory yang udah kelihatan, itu opsi yang belum mulai. Instingmu lebih tajam dari contoh yang saya karang.

---

## 2. Ini Menjawab Pertanyaan Terbuka Kemarin: Bagian 3 Dipecah

Trajectory butuh dua bahan yang beda sifat: **struktur** (seberapa besar TAM-nya, posisi kompetitif macam apa) dan **momentum** (pertumbuhan segmen sekarang, guidance vs konsensus).

Quality butuh yang pertama doang. Multibagger butuh dua-duanya.

**Keputusan: bagian 3 Knowledge dipecah.**

| | Isi | Multibagger | Quality |
|---|---|:---:|:---:|
| **3a — Struktur** | model bisnis, konsentrasi revenue, ukuran TAM, posisi kompetitif | ✅ | ✅ |
| **3b — Momentum** | pertumbuhan segmen, tren guidance vs konsensus, akselerasi/deselerasi | ✅ | ❌ |

Ini mengunci temuan MSFT kemarin, bukan membatalkannya: Quality **tetap nggak boleh** lihat Azure +40% (itu 3b). Yang dia lihat cuma margin GM 67.6% (tersempit sejak 2022) dan capex $190B yang seperdelapannya cuma bayar harga komponen lebih mahal (bagian 2 + 3a). `compounding_rapuh` tetap keluar — bukan karena saya nggak ngasih Quality lihat kabar baiknya, tapi karena kabar baiknya memang bukan kategori yang Quality tanya.

---

## 3. Kriteria Tiap Modul — Konkret, Bukan Vibe

### Quality / Compound
**Baca:** 1, 2 (penuh), 3a, 4 (penuh), 5, 6, 7, peer. **Nggak baca:** 3b, `CatalystSet`.

| Stance | Syarat |
|---|---|
| `compounding_kuat` | Margin (gross/operating) stabil atau naik N periode terakhir · FCF positif berturut · konsentrasi revenue di bawah ambang · percentile margin peer di atas median · tanpa flag governance severity tinggi yang belum direspons |
| `compounding_rapuh` | Margin/FCF mulai turun, TAPI model bisnis & posisi kompetitif (3a) masih utuh — mesin melambat, belum berubah bentuk |
| `bukan_compounder` | Kerugian struktural di bisnis inti, ATAU restatement, ATAU margin & FCF dua-duanya negatif bersamaan — mesin sudah jadi mesin lain |
| `mesin_tak_terbaca` | Field kunci di 2/3a/7 `missing` melewati ambang |

*(MSFT: margin turun + capex naik 61% dengan 1/8 cuma inflasi harga → `compounding_rapuh`, bukan `bukan_compounder` — model bisnisnya (3a) masih utuh, cuma mesinnya lagi mahal dijalanin.)*

### Multibagger
**Baca:** 1, 2 (arah saja), 3a, 3b, 4, 6, peer, `CatalystSet` (sekunder). **Nggak baca:** 5, 7.

| Stance | Syarat |
|---|---|
| `ruang_terbuka` | Pertumbuhan segmen (3b) berakselerasi/infleksi positif · valuasi sekarang cuma masuk akal kalau diasumsikan pertumbuhan segera melambat · TAM (3a) masih ada headroom (peer group nunjukin penetrasi/pangsa masih rendah) |
| `ruang_sempit` | Pertumbuhan kuat, TAPI valuasi (peer percentile) udah tinggi — kelanjutannya udah mulai di-price |
| `ruang_tertutup` | Pertumbuhan datar/melambat DAN TAM matang (peer group semua mapan, market-nya sendiri nggak tumbuh) |
| `ruang_tak_terbaca` | 3b `missing`, atau peer group `low_sample_size` |

*(PLTR/TSM versi kamu: pertumbuhan segmen jelas berakselerasi + TAM AI/foundry masih headroom besar → `ruang_terbuka`. INTC versi saya: $174M dari basis nyaris nol bukan akselerasi yang udah kelihatan → lebih pas `ruang_tak_terbaca` atau `ruang_sempit`, bukan `ruang_terbuka` seperti saya klaim kemarin. Saya revisi klaim saya sendiri di sini.)*

### Speculative
**Baca:** 1, volatilitas (4), 5, `CatalystSet` (penuh), konteks makro. **Nggak baca:** 2, 3, 6.

| Stance | Syarat |
|---|---|
| `asimetri_berkatalis` | Minimal satu katalis `scheduled`/`expected` dalam horizon · hasil katalis genuinely nggak pasti (ada gap guidance vs konsensus, atau outcome biner regulasi/trial) · volatilitas cukup buat asimetri berarti |
| `asimetri_tanpa_katalis` | Nggak ada katalis dalam horizon, tapi volatilitas tetap tinggi |
| `tanpa_asimetri` | Nggak ada katalis, volatilitas rendah |
| `asimetri_tak_terbaca` | `CatalystSet.status` degraded/missing |

**Beda dari Multibagger meski sama-sama baca katalis:** Speculative nanya *"katalis ini resolve gimana"* — perlu tanggal. Multibagger nanya *"kurvanya lanjut nggak"* — nggak perlu tanggal, katalis cuma bumbu.

---

## 4. Yang Saya Nggak Berani Putuskan Sendiri

- **Ambang numerik** (berapa persen margin turun baru `compounding_rapuh`, berapa lama pertumbuhan flat baru `ruang_tertutup`) — ini butuh diuji lewat lebih banyak saham, bukan diputuskan dari kursi.
- **Bagian 3a/3b ini pecahan yang benar,** atau kamu punya batas lain di kepala yang belum keluar. Cara ngeceknya: coba tebak treatment KO vs COST pakai kriteria di atas, terus bandingin sama insting kamu. Kalau meleset, itu bagian 3a/3b-nya yang salah gambar, bukan angkanya.

## 5. Langkah Berikutnya

Kalau kriteria ini kerasa "kok gitu doang" atau "bukan ini yang saya maksud" di bagian manapun — **itu sinyal, bukan formalitas**. Bilang di bagian mana. Kalau kerasa benar, saya formalkan jadi D-13 di repo dan kita run sesi buat lihat angka *surprise*-nya beneran nangkep sesuatu apa nggak.

---

© AlphaForge v2 — draft kerja, dua penulis
