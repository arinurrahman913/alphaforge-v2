# AlphaForge v2 — Glosarium

**Status:** Aktif

Istilah-istilah kunci yang dipakai konsisten di seluruh dokumen AlphaForge v2.

---

**Market Context Package**
Kumpulan hasil dari 12 komponen Layer 1, digabung jadi satu paket konteks yang dikonsumsi oleh Layer 2 saat menganalisa saham individual.

**Screening**
Tahap awal Layer 2 yang menyaring seluruh market (NASDAQ + NYSE) untuk mempersempit ribuan ticker menjadi kandidat yang layak diproses lebih dalam.

**Evidence**
Fakta mentah yang sudah diverifikasi tentang satu saham — data keuangan, harga, kepemilikan institusional, berita. Belum ada interpretasi di tahap ini.

**Knowledge**
Pemahaman terstruktur yang dibangun dari Evidence. Ini bukan lagi data mentah, tapi rangkuman/profil yang siap dipakai sebagai bahan reasoning.

**Risk / Red-Flag Check**
Gerbang pemeriksaan yang mendeteksi masalah serius (akuntansi, governance, litigasi) sebelum Knowledge diteruskan ke modul reasoning manapun.

**Confidence / Data Quality Score**
Ukuran seberapa kuat/lengkap evidence yang mendasari sebuah kesimpulan. Membedakan kesimpulan yang didukung data solid dari kesimpulan yang didasari data tipis.

**Multibagger Module**
Salah satu dari tiga modul reasoning di Layer 2. Fokus mencari saham dengan potensi pertumbuhan eksplosif jangka panjang.

**Quality/Compound Module**
Modul reasoning yang fokus pada saham berkualitas tinggi dengan moat kuat dan kinerja yang konsisten dari waktu ke waktu (gaya compounder).

**Speculative Module**
Modul reasoning yang fokus pada peluang momentum/katalis dengan profil risk/reward asimetris, termasuk saham yang secara fundamental belum solid tapi punya katalis besar.

**Catalyst**
Peristiwa spesifik yang diperkirakan menggerakkan harga saham — laporan earnings, peluncuran produk, persetujuan regulasi, dsb. Terutama relevan untuk Speculative Module.

**Aggregator**
Komponen akhir Layer 2 yang menyusun hasil dari ketiga modul reasoning untuk ditampilkan berdampingan — tidak menggabungkannya menjadi satu skor tunggal.

**Historical Tracking / Decision Journal**
Mekanisme penyimpanan hasil analisa dari waktu ke waktu, dipakai untuk memvalidasi apakah kesimpulan sistem terbukti akurat di kemudian hari.

**Business Cycle Stage**
Posisi ekonomi secara fundamental (early-cycle, mid-cycle, late-cycle, recession) — salah satu komponen Layer 1, menjadi dasar konseptual untuk Sector Rotation.

**Market Regime**
Kondisi teknikal market secara umum (bull/bear/sideways), diukur dari posisi index terhadap moving average jangka panjang.

**Market Breadth**
Ukuran kesehatan sebuah rally — berapa banyak saham individual yang ikut naik/turun, bukan hanya pergerakan index secara agregat.

**Derived/Approximated Data**
Data yang tidak tersedia lewat satu API siap pakai, tapi dikonstruksi dari kombinasi beberapa sumber data mentah gratis.

---

© AlphaForge v2
