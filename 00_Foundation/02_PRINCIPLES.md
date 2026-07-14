# AlphaForge v2 — Prinsip

**Status:** Aktif

Dokumen ini adalah aturan main yang berlaku di semua layer dan modul. Kalau ada keputusan desain yang bertentangan dengan salah satu prinsip di bawah, prinsip ini yang menang.

---

## 1. Evidence Sebelum Knowledge

Tidak ada kesimpulan yang dibangun tanpa fakta yang mendasarinya lebih dulu. Urutannya selalu: kumpulkan bukti terverifikasi → baru bentuk pemahaman terstruktur dari bukti itu. Ini kebalikan dari urutan di spec engine v1, dan sengaja dibalik di v2.

## 2. Context Sebelum Analysis

Tidak ada saham yang dianalisa dalam ruang hampa. Kondisi market, sektor, dan makro (Layer 1) selalu dibaca lebih dulu, dan hasilnya menjadi konteks yang menyertai setiap analisa saham individual di Layer 2.

## 3. Multi-Lens, Bukan Single-Verdict

Satu saham bisa punya profil yang berbeda tergantung kacamata investasi yang dipakai. AlphaForge tidak memaksa satu kesimpulan tunggal — tiga modul (Multibagger, Quality/Compound, Speculative) bereasoning secara independen, dan hasilnya ditampilkan berdampingan. Investor yang memilih lensa mana yang relevan dengan tujuannya.

## 4. Risk Adalah Gerbang, Bukan Sekadar Skor

Red flag serius (masalah akuntansi, governance, litigasi) bukan salah satu dari banyak faktor yang dirata-rata ke dalam skor — itu adalah pemeriksaan yang berlaku ke semua modul reasoning sebelum mereka jalan lebih jauh.

## 5. Confidence Itu Eksplisit

Setiap kesimpulan membawa informasi seberapa kuat evidence yang mendasarinya. Data yang tipis atau tidak lengkap harus terlihat tipis — tidak disamarkan seolah sama meyakinkannya dengan data yang solid.

## 6. Sistem Harus Bisa Diaudit ke Belakang

Kesimpulan yang dihasilkan hari ini harus bisa dicek ulang di masa depan terhadap apa yang benar-benar terjadi. Tanpa histori yang terlacak, tidak ada cara membuktikan apakah reasoning-nya benar-benar akurat atau sekadar terdengar meyakinkan.

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
