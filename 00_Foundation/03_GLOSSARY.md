# AlphaForge v2 — Glosarium

**Status:** Aktif — revisi untuk kelengkapan cakupan & konsistensi dengan `02_PRINCIPLES.md`

Istilah-istilah kunci yang dipakai konsisten di seluruh dokumen AlphaForge v2. Disusun per kelompok: konsep fondasi, istilah Layer 1, istilah Layer 2.

---

## Konsep Fondasi

**Market Context Package**
Kumpulan hasil dari 12 komponen Layer 1, digabung jadi satu paket konteks yang dikonsumsi oleh Layer 2 saat menganalisa saham individual. Dihitung sekali per sesi analisa, bukan per saham.

**Derived/Approximated Data**
Data yang tidak tersedia lewat satu API siap pakai, tapi dikonstruksi dari kombinasi beberapa sumber data mentah gratis (misal Business Cycle Stage, Money Flow, Market Sentiment). Sesuai Prinsip #5 (`02_PRINCIPLES.md`), komponen berkategori ini wajib secara eksplisit menyatakan dirinya sebagai pembacaan yang dikonstruksi/didekati — bukan angka resmi tunggal dari satu sumber otoritatif — supaya tidak keliru dianggap sekuat data langsung seperti VIX atau DXY.

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

---

© AlphaForge v2
