# AlphaForge v2 — Prinsip

**Status:** Aktif — revisi untuk konsistensi dengan spec turunan (lihat catatan di Prinsip #4, #5, #6)
**Doc version:** 1.2.0

Dokumen ini adalah aturan main yang berlaku di semua layer dan modul. Keputusan desain yang diambil di bawah prinsip-prinsip ini — beserta alternatif yang ditolak — dicatat di `04_DECISIONS.md`. Kalau ada keputusan desain yang bertentangan dengan salah satu prinsip di bawah, prinsip ini yang menang, kecuali pengecualian yang secara eksplisit dicatat di prinsip terkait (lihat Prinsip #6).

---

## 1. Evidence Sebelum Knowledge

Tidak ada kesimpulan yang dibangun tanpa fakta yang mendasarinya lebih dulu. Urutannya selalu: kumpulkan bukti terverifikasi → baru bentuk pemahaman terstruktur dari bukti itu. Ini kebalikan dari urutan di spec engine v1, dan sengaja dibalik di v2.

## 2. Context Sebelum Analysis

Tidak ada saham yang dianalisa dalam ruang hampa. Kondisi market, sektor, dan makro (Layer 1) selalu dibaca lebih dulu, dan hasilnya menjadi konteks yang menyertai setiap analisa saham individual di Layer 2.

## 3. Multi-Lens, Bukan Single-Verdict

Satu saham bisa punya profil yang berbeda tergantung kacamata investasi yang dipakai. AlphaForge tidak memaksa satu kesimpulan tunggal — tiga modul (Multibagger, Quality/Compound, Speculative) bereasoning secara independen, dan hasilnya ditampilkan berdampingan. Investor yang memilih lensa mana yang relevan dengan tujuannya.

"Independen" di sini bukan sekadar klaim konseptual, tapi syarat teknis minimum: ketiga modul menerima input yang sama (Knowledge + Market Context Package), tidak saling memanggil, tidak berbagi state satu sama lain dalam satu sesi analisa, dan urutan eksekusinya tidak boleh mempengaruhi hasil modul lain. Kalau implementasi nanti membuat ketiganya berbagi komponen (misalnya prompt/template reasoning yang sama), komponen bersama itu harus dipastikan tidak diam-diam menyamakan kesimpulan ketiganya — kalau itu terjadi, Multi-Lens jadi klaim di atas kertas saja.

Syarat teknis di atas sekarang punya cara dicek, bukan cuma dinyatakan: tes **T1** (urutan eksekusi acak → output harus identik) dan **T2** (konvergensi stance >90% → wajib review) di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

## 4. Risk Adalah Gerbang, Bukan Sekadar Skor

Red flag serius (masalah akuntansi, governance, litigasi) bukan salah satu dari banyak faktor yang dirata-rata ke dalam skor. "Gerbang" di sini berarti **red flag serius wajib menempel eksplisit dan terlihat oleh ketiga modul reasoning sebelum mereka menyimpulkan apa pun** — bukan tenggelam sebagai satu dari banyak input yang dirata-rata secara diam-diam.

Ini bukan berarti proses berhenti begitu ada red flag. Saham tetap diproses sampai ke tiga modul reasoning; yang tidak boleh terjadi adalah modul reasoning menyimpulkan tanpa mempertimbangkan red flag itu. Implementasinya: flag menempel ke Knowledge dan wajib direspons eksplisit oleh tiap modul (misalnya lewat pembatasan confidence atau catatan reasoning), bukan sekadar disediakan sebagai data yang boleh diabaikan.

"Wajib" di sini ditegakkan secara mekanis, bukan diharapkan: output modul yang jumlah `flag_responses`-nya tidak cocok dengan jumlah flag severity tinggi **ditolak** (aturan V1), dan rationale yang identik untuk dua flag berbeda ditandai untuk review (V2). Lihat `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6. Prinsip yang tidak bisa gagal secara mekanis akan pelan-pelan jadi hiasan.

Pengecualian: kasus yang sudah terkonfirmasi sangat berat (misalnya fraud yang telah terbukti/diputuskan otoritas berwenang, delisting resmi) boleh menghentikan proses lebih awal di Risk/Red-Flag Check — tapi ini kasus khusus, bukan perilaku default gerbang ini. Detail ambang mana yang menghentikan proses vs. yang hanya menempel sebagai flag didefinisikan di `03_LAYER2_SPECS/04_RISK_REDFLAG_CHECK.md`.

## 5. Confidence Itu Eksplisit

Setiap kesimpulan **tentang saham individual** (Knowledge, hasil tiga modul reasoning, Output) membawa informasi seberapa kuat evidence yang mendasarinya. Data yang tipis atau tidak lengkap harus terlihat tipis — tidak disamarkan seolah sama meyakinkannya dengan data yang solid. Mekanisme skornya ada di `03_LAYER2_SPECS/05_CONFIDENCE_DATA_QUALITY.md`.

Komponen Layer 1 yang sifatnya derived/approximated (Business Cycle Stage, Money Flow, Market Sentiment — lihat `04_DATA_SOURCES/01_PROVIDERS_OVERVIEW.md`) tunduk pada semangat prinsip yang sama meski belum punya skor formal: setiap komponen begini harus secara eksplisit menyatakan dirinya sebagai pembacaan yang dikonstruksi/didekati, bukan angka resmi tunggal dari satu sumber otoritatif — supaya Layer 2 dan pengguna tidak keliru menganggapnya sekuat data langsung seperti VIX atau DXY.

Sejak revisi kontrak data, pernyataan ini tidak lagi berhenti di dokumen: field `kind` (`direct`/`derived`) menempel di tiap `ComponentReading` dan ikut mengalir ke Layer 2, sehingga modul reasoning bisa memperlakukan keduanya berbeda secara programatis.

## 6. Sistem Harus Bisa Diaudit ke Belakang

Kesimpulan yang dihasilkan hari ini harus bisa dicek ulang di masa depan terhadap apa yang benar-benar terjadi. Tanpa histori yang terlacak, tidak ada cara membuktikan apakah reasoning-nya benar-benar akurat atau sekadar terdengar meyakinkan.

Setiap entri historis yang disimpan (lihat `03_LAYER2_SPECS/12_HISTORICAL_TRACKING_JOURNAL.md`) juga harus mencatat **versi metodologi/formula** yang dipakai saat kesimpulan itu dibuat — terutama untuk komponen derived/approximated yang logikanya bisa berubah seiring iterasi (lihat Prinsip #5). Tanpa ini, audit di masa depan berisiko membandingkan kesimpulan lama dengan formula yang sudah berbeda tanpa disadari, dan validitas perbandingannya jadi bias.

**Pengecualian yang diakui secara sadar:** **evaluasi** Historical Tracking/Decision Journal boleh menyusul (v2.1), setelah alur inti Screening→Output berjalan. **Penyimpanan** snapshot-nya tidak ikut ditunda — ia jalan sejak v2.0, karena biayanya mendekati nol dan tanpanya v2.1 tidak punya apa-apa untuk dievaluasi. Menunda keduanya berarti jam pembuktian baru mulai berjalan berbulan-bulan setelah klaim "v2 lebih baik dari v1" dibuat. Ini satu-satunya pengecualian eksplisit terhadap aturan "prinsip selalu menang atas keputusan desain" di dokumen ini — ditulis di sini secara sadar, bukan dibiarkan jadi kontradiksi implisit dengan status "Aktif" yang dipakai prinsip-prinsip lain.

## 7. Gratis Dulu, Berbayar Belakangan

Semua sumber data diusahakan gratis dulu — baik lewat API langsung maupun dikonstruksi dari kombinasi data mentah gratis. Kebutuhan data berbayar baru dipertimbangkan kalau memang tidak ada cara lain yang masuk akal.

## 8. Kedalaman di Atas Pelebaran

Setiap komponen baru yang diusulkan untuk masuk ke Layer 1 atau Layer 2 harus dicek dulu: apakah ini benar-benar hal baru, atau varian dari yang sudah ada? Ruang lingkup yang dijaga ketat lebih baik daripada sistem yang terus melebar tanpa kendali.

## 9. v1 Adalah Referensi, Bukan Beban

v2 dibangun dari nol secara konseptual, tapi tidak dengan mengabaikan pelajaran dari v1. Pola yang terbukti bekerja di v1 (struktur CLI, provider pattern) boleh dipakai lagi; pola yang terbukti bermasalah (Knowledge-before-Evidence, single verdict) sengaja tidak dibawa.

## 10. AlphaForge Membantu Berpikir, Bukan Menggantikan Keputusan

Output sistem ini adalah bahan pertimbangan yang terstruktur dan bisa dijelaskan — bukan sinyal beli/jual otomatis. Keputusan akhir selalu ada di tangan investor.

---

© AlphaForge v2
