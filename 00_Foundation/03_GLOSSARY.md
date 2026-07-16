# AlphaForge v2 — Glosarium

**Status:** Aktif — revisi: istilah dari kontrak data & keputusan D-01..D-05 ditambahkan
**Doc version:** 1.5.0

Istilah-istilah kunci yang dipakai konsisten di seluruh dokumen AlphaForge v2. Disusun per kelompok: konsep fondasi, istilah Layer 1, istilah Layer 2.

---

## Konsep Fondasi

**Market Context Package**
Kumpulan hasil dari 12 komponen Layer 1, digabung jadi satu paket konteks yang dikonsumsi oleh Layer 2 saat menganalisa saham individual. Dihitung sekali per sesi analisa, bukan per saham.

**Derived/Approximated Data**
Data yang tidak tersedia lewat satu API siap pakai, tapi dikonstruksi dari kombinasi beberapa sumber data mentah gratis (misal Business Cycle Stage, Money Flow, Market Sentiment). Sesuai Prinsip #5 (`02_PRINCIPLES.md`), komponen berkategori ini wajib secara eksplisit menyatakan dirinya sebagai pembacaan yang dikonstruksi/didekati — bukan angka resmi tunggal dari satu sumber otoritatif — supaya tidak keliru dianggap sekuat data langsung seperti VIX atau DXY.

**Method Version**
Versi *formula* sebuah komponen (semver), beda dari versi dokumennya. Wajib untuk komponen derived/approximated, dan wajib ikut tercatat di tiap entri Historical Tracking (Prinsip #6). Perubahan logika yang mengubah hasil wajib menaikkannya — tanpa ini, audit di masa depan bisa membandingkan kesimpulan lama dengan formula yang sudah berbeda tanpa disadari.

**Kind (`direct` / `derived`)**
Penanda yang menempel di tiap pembacaan komponen Layer 1. `direct` = satu angka dari satu sumber otoritatif (VIX, DXY). `derived` = dikonstruksi sendiri dari kombinasi sumber (Business Cycle Stage, Money Flow, Market Breadth, Market Sentiment). Ada supaya Prinsip #5 ikut mengalir bersama datanya ke Layer 2, bukan berhenti sebagai kalimat di dokumen spec. Lihat `01_ARCHITECTURE/04_DATA_CONTRACTS.md`.

---

## Istilah Layer 1 — Market Context Engine

**Business Cycle Stage**
Posisi ekonomi secara fundamental (early-cycle, mid-cycle, late-cycle, recession) — menjadi dasar konseptual untuk Sector Rotation. Derived/approximated dari kombinasi indikator FRED (PMI, GDP, unemployment).

**Sector Rotation**
Pergerakan relatif dana antar sektor — sektor mana yang sedang diminati (inflow) dan mana yang ditinggalkan (outflow), diukur dari kinerja relatif sector ETF terhadap index acuan.

**Money Flow**
Estimasi arah aliran dana secara agregat di level sektor/market (bukan per saham individual — money flow di level saham ada di data institutional ownership pada Evidence, Layer 2). Derived/approximated dari proxy volume & price action di sector ETF, karena data flow granular resmi (EPFR, Lipper) berbayar.

**Liquidity Conditions**
Kondisi likuiditas sistem keuangan secara luas — seberapa banyak "uang" yang tersedia mengalir ke aset berisiko. Dibaca dari tren Fed balance sheet, M2 money supply, dan credit spread.

**Yield Curve**
Struktur suku bunga obligasi pemerintah AS dari berbagai tenor. Bentuknya (normal vs inverted) jadi indikator resesi yang diawasi, dan memengaruhi biaya modal & valuasi relatif growth vs value.

**Market Breadth**
Ukuran kesehatan sebuah rally — berapa banyak saham individual yang benar-benar ikut naik/turun, bukan hanya pergerakan index secara agregat. Derived/approximated dari advance/decline ratio dan persentase saham di atas MA200 seluruh konstituen index.

**Volatility Index (VIX)**
Indikator standar ekspektasi volatilitas pasar (risk appetite) berdasarkan harga opsi S&P 500. VIX rendah menandakan risk-on/complacent; VIX tinggi menandakan fear/risk-off.

**Market Regime**
Kondisi teknikal market secara umum (bull/bear/sideways), diukur dari posisi index utama terhadap moving average jangka panjang (MA50, MA200).

**Macro Economic Calendar**
Jadwal rilis data ekonomi penting yang biasa menggerakkan market — CPI, keputusan FOMC, laporan tenaga kerja (NFP). Ditarik dari FRED release calendar, bukan versi scraping situs pihak ketiga.

**Currency / Dollar Index (DXY)**
Indeks kekuatan dolar AS relatif terhadap sekeranjang mata uang utama lainnya. Memengaruhi saham dengan eksposur global atau komoditas.

**Commodity Signals**
Pergerakan harga komoditas utama (emas, minyak) sebagai indikator kondisi makro dan sentimen risk-on/risk-off.

**Market Sentiment**
Ukuran sentimen investor secara umum (greed/fear) di luar ukuran volatilitas murni seperti VIX. Derived/approximated dari kombinasi AAII survey, put/call ratio, VIX, dan breadth — bukan versi identik dari index komersial manapun.

---

## Istilah Layer 2 — Stock Analysis Engine

**Screening**
Tahap awal Layer 2 yang menyaring seluruh market (NASDAQ + NYSE) untuk mempersempit ribuan ticker menjadi kandidat yang layak diproses lebih dalam ke Evidence.

**Hard Exclude**
Kriteria di tahap Screening yang membuat saham langsung tidak lanjut diproses sama sekali (misal bukan saham biasa, market cap di bawah ambang, data fundamental tidak tersedia). Dibatasi hanya untuk hal yang membuat analisa secara struktural tidak mungkin — lihat `03_LAYER2_SPECS/01_SCREENING.md`.

**Soft Flag**
Kriteria di tahap Screening yang tetap meloloskan saham ke Evidence, tapi menandainya (misal `micro_cap`, `adr`, `recent_ipo`, `low_liquidity`) untuk dipertimbangkan Confidence Score dan modul reasoning di tahap berikutnya. Beda dari Hard Exclude — saham berisiko tapi tetap bisa dianalisa tidak digugurkan di Screening.

**Evidence**
Fakta mentah yang sudah diverifikasi tentang satu saham — data keuangan, harga, kepemilikan institusional, berita. Belum ada interpretasi di tahap ini.

**Knowledge**
Pemahaman terstruktur yang dibangun dari Evidence. Berisi fakta turunan (rasio, tren, kategori berbasis ambang) — bukan penilaian kualitatif ("bagus", "berisiko"), karena penilaian itu wewenang tiga modul reasoning, bukan Knowledge (lihat `03_LAYER2_SPECS/03_KNOWLEDGE.md`).

**Peer / Relative Comparison**
Proses membandingkan suatu saham dengan kompetitor/industri sejenis, supaya jelas apakah suatu angka "bagus" secara absolut atau relatif. Beroperasi di atas Knowledge, bukan Evidence mentah — hasilnya jadi input tambahan terutama untuk Quality/Compound Module.

**Risk / Red-Flag Check**
Pemeriksaan yang mendeteksi masalah serius (akuntansi, governance, litigasi) pada Knowledge sebelum diteruskan ke modul reasoning. Sesuai Prinsip #4 (revisi, `02_PRINCIPLES.md`): flag serius menempel eksplisit dan wajib direspons oleh ketiga modul reasoning — bukan menghentikan proses secara default. Pengecualian berlaku untuk kasus yang sudah terkonfirmasi sangat berat (fraud terbukti, delisting resmi), yang boleh menghentikan proses lebih awal.

**Confidence / Data Quality Score**
Ukuran seberapa kuat/lengkap evidence yang mendasari sebuah kesimpulan tentang saham individual (Knowledge, hasil tiga modul reasoning, Output). Membedakan kesimpulan yang didukung data solid dari kesimpulan yang didasari data tipis. Cakupannya khusus per-saham di Layer 2 — komponen Layer 1 derived/approximated tunduk pada semangat yang sama tapi lewat mekanisme berbeda (lihat Prinsip #5).

**Multibagger Module**
Salah satu dari tiga modul reasoning independen di Layer 2. Fokus mencari saham dengan potensi pertumbuhan eksplosif jangka panjang.

**Quality/Compound Module**
Modul reasoning independen yang fokus pada saham berkualitas tinggi dengan moat kuat dan kinerja yang konsisten dari waktu ke waktu (gaya compounder).

**Speculative Module**
Modul reasoning independen yang fokus pada peluang momentum/katalis dengan profil risk/reward asimetris, termasuk saham yang secara fundamental belum solid tapi punya katalis besar.

**Catalyst**
Peristiwa spesifik yang diperkirakan menggerakkan harga saham — laporan earnings, peluncuran produk, persetujuan regulasi, dsb. Terutama relevan untuk Speculative Module.

**Catalyst Tracking**
Mekanisme yang mengidentifikasi Catalyst mendatang untuk suatu saham beserta estimasi tanggal dan potensi dampaknya. Dipakai terutama oleh Speculative Module sebagai bagian dari reasoning-nya — lihat `03_LAYER2_SPECS/10_CATALYST_TRACKING.md`.

**Aggregator**
Komponen akhir Layer 2 yang menyusun hasil dari ketiga modul reasoning untuk ditampilkan berdampingan — tidak menggabungkannya menjadi satu skor tunggal. Menyertakan Confidence Score dan flag Risk/Red-Flag Check secara eksplisit di output.

**Historical Tracking / Decision Journal**
Mekanisme penyimpanan hasil analisa dari waktu ke waktu, dipakai untuk memvalidasi apakah kesimpulan sistem terbukti akurat di kemudian hari. Sesuai Prinsip #6 (revisi, `02_PRINCIPLES.md`), setiap entri historis harus turut mencatat versi metodologi/formula yang dipakai saat itu, khususnya untuk komponen derived/approximated. Implementasinya boleh menyusul (v2.1) setelah alur inti Screening→Output berjalan — ini pengecualian yang diakui secara eksplisit di Prinsip #6, bukan berarti komponennya kurang penting.

**Fase A / Fase B & Barrier**
Layer 2 berjalan dua fase, bukan satu alur lurus. **Fase A** (Evidence → Knowledge) berjalan per-ticker dan bisa paralel penuh. **Barrier** adalah titik tunggu sampai Knowledge seluruh kandidat selesai. **Fase B** (Peer → Confidence → Risk → 3 modul → Aggregator) butuh populasi utuh karena Peer membandingkan antar ticker. Barrier ini konsekuensi tak terhindarkan dari memakai populasi hasil screening sendiri sebagai sumber peer — lihat D-03 di `00_Foundation/04_DECISIONS.md`.

**Module Output**
Bentuk baku hasil tiap modul reasoning: `stance`, `confidence`, `flag_responses`, `context_used`, `knowledge_gaps`. **Identik untuk ketiga modul** — wadah yang seragam justru yang membuat tiga pandangan bisa ditampilkan berdampingan tanpa harus disamakan isinya. Yang wajib berbeda antar modul adalah kriteria dan kesimpulannya, bukan struktur laporannya. Dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

**Flag Response**
Entri wajib di `ModuleOutput` untuk **setiap** flag severity tinggi yang menempel di Knowledge. Berisi `flag_id`, `impact`, dan `rationale` yang spesifik ke flag itu. Inilah yang membuat "wajib direspons" di Prinsip #4 bisa gagal secara mekanis: output yang jumlah flag_response-nya tidak cocok akan ditolak, bukan sekadar dianggap kurang rapi.

**Undetermined (Risk)**
Status pemeriksaan red flag saat data governance yang dibutuhkan `missing`. Beda tegas dari "tidak ada red flag": yang satu berarti tidak ketemu masalah, yang satu berarti tidak sempat melihat. Modul reasoning berhak tahu yang mana.

---

## Istilah Dashboard

**Dashboard Lokal**
Lapisan tampilan untuk hasil kedua layer. Berjalan lokal tanpa server, membaca artefak dari disk. Aturan mengikatnya: **menampilkan, tidak menghitung** — dashboard yang menghitung sendiri jadi sumber kebenaran kedua, dan yang diaudit nanti adalah yang tersimpan, bukan yang tampil.

**Context Summary**
Kesimpulan kondisi market dari 12 komponen Layer 1 (Section 2 halaman Layer 1). **Dihasilkan Layer 1, bukan dashboard** — meringkas 12 pembacaan adalah sintesis, dan sintesis adalah reasoning: ia butuh `method_version`, confidence, dan jejak audit. Dilarang punya skor tunggal. Lihat D-06.

**Masuk Lewat Lensa**
Cara satu-satunya sampai ke halaman saham: tiga daftar terpisah (satu per modul), masing-masing diurutkan menurut kosakatanya sendiri, plus daftar Divergensi. Tidak pernah digabung. Dasarnya: **Prinsip #3 melarang MENGGABUNG lensa, bukan MENGURUTKAN di dalam satu lensa** — pembedaan yang selama ini tidak pernah dinyatakan. Lihat D-08.

**Kosakata Stance**
Tiap modul punya kosakata `stance` sendiri yang diturunkan dari pertanyaannya sendiri (`ruang_terbuka` vs `compounding_kuat` vs `asimetri_berkatalis`). Enum bersama yang lama (`compelling/interesting/weak`) adalah skala ordinal yang langsung berubah jadi hitungan suara. Kosakata terpisah membuat suara **tidak bisa dihitung karena operasinya tidak ada**. Pemetaan antar kosakata dilarang dibuat. Lihat D-09.

**Catalyst Set**
Katalis terjadwal per ticker, dihasilkan Fase A. **Bukan bagian Knowledge** — alasannya umur simpan: isi Knowledge meluruh serempak dari satu `evidence_snapshot_date`, katalis punya kedaluwarsa masing-masing. Lihat D-11.

**Confidence Report vs Confidence Modul**
`ConfidenceReport` (1 per ticker) mengukur kekuatan **data**. `ModuleOutput.confidence` (3 per ticker) mengukur keyakinan **modul pada kesimpulannya sendiri**. Aturan V6: yang kedua tidak boleh melebihi yang pertama. Sebaliknya boleh — data lengkap tidak mewajibkan kesimpulan kuat.

**Synthesis (Peta Konvergensi)**
Bagian yang memetakan di mana ketiga modul reasoning sepakat, di mana berbeda, dan **kenapa** berbeda (`root_cause`). Bukan verdict: verdict memampatkan tiga dimensi jadi satu lalu membuang sisanya; `synthesis` menunjuk balik ke tiga hasil dan tidak menggantikannya — daftar isi, bukan pengganti isi. Confidence-nya = terendah dari tiga modul, bukan rata-rata. Ditampilkan di bawah tiga kolom, tidak di atas. Uji pembedanya: kalau bisa dibaca sendirian dan terasa cukup, ia sudah jadi verdict. Lihat D-07.

**Narasi (Narrative)**
Penjelasan arti sebuah pembacaan, dalam bahasa manusia. **Artefak pipeline, bukan hasil render** — narasi yang disusun saat halaman dibuka tidak berversi, tidak tersimpan, dan bisa berbeda tiap kali dibuka untuk data yang sama.

---

© AlphaForge v2
