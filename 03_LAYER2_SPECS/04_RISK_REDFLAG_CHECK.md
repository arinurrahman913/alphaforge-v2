# Risk / Red-Flag Check

**Status:** Aktif — revisi untuk konsistensi dengan Prinsip #4 (`00_Foundation/02_PRINCIPLES.md`)

## Definisi

Pemeriksaan yang mendeteksi masalah serius pada suatu saham sebelum Knowledge diteruskan ke modul reasoning. Bukan gerbang yang menghentikan proses secara default — lihat bagian "Cara Kerja" untuk definisi operasional "gerbang" yang dipakai di sini.

## Kenapa Penting

Tanpa pemeriksaan eksplisit, risiko besar (kecurangan akuntansi, dilusi ekstrem, litigasi berat) bisa 'tenggelam' jadi salah satu dari banyak faktor yang dirata-rata — padahal seharusnya jadi peringatan yang tidak bisa diabaikan oleh modul reasoning manapun.

## Sumber Data / Pendekatan

Diturunkan dari Knowledge & Evidence — mencari pola seperti auditor berganti-ganti, restatement laporan keuangan, insider selling besar-besaran, dilusi saham berlebihan.

## Kategori Red Flag (Ilustratif — Perlu Dilengkapi Saat Implementasi)

Dipecah jadi dua tingkat severity, konsisten dengan Prinsip #4:

**Severity Tinggi — Wajib Direspons, Tidak Menghentikan Proses secara Default**
- Auditor berganti > 1 kali dalam 3 tahun terakhir
- Restatement laporan keuangan dalam 2 tahun terakhir
- Insider selling besar-besaran (nilai/persentase kepemilikan di atas ambang yang perlu dikalibrasi saat implementasi) dalam 90 hari terakhir
- Dilusi saham berlebihan (peningkatan shares outstanding di atas ambang tertentu dalam 12 bulan)
- Litigasi material yang sedang berjalan (class action, investigasi regulator)

**Severity Ekstrem — Pengecualian yang Boleh Menghentikan Proses Lebih Awal**
- Fraud akuntansi yang telah dikonfirmasi/diputuskan otoritas berwenang (SEC, pengadilan)
- Delisting resmi atau proses kebangkrutan (Chapter 11) yang telah diajukan

Daftar ini ilustratif dan perlu divalidasi/dikalibrasi lebih detail sebelum implementasi — ambang kuantitatif spesifik (persentase, nilai dolar, jangka waktu) belum final.

## Cara Kerja

Jalankan serangkaian pemeriksaan pola red flag terhadap Knowledge, hasilkan flag dengan tingkat severity di atas.

**Untuk flag Severity Tinggi:** flag ditempelkan ke Knowledge sebagai data terstruktur (kategori, severity, evidence pendukung) yang terlihat oleh ketiga modul reasoning. "Wajib direspons" berarti tiap modul harus menyatakan secara eksplisit bagaimana flag ini memengaruhi kesimpulannya — baik lewat pembatasan confidence, catatan reasoning yang menyebutnya secara spesifik, atau penyesuaian penilaian. Modul tidak boleh menghasilkan kesimpulan yang diam-diam mengabaikan flag yang ada.

**Untuk flag Severity Ekstrem:** proses berhenti di titik ini — saham tetap tercatat (dengan alasan berhenti tercantum), tapi tidak diteruskan ke tiga modul reasoning. Ini pengecualian eksplisit terhadap perilaku default "tidak menghentikan proses" di atas.

## Terhubung Dengan

Berlaku ke ketiga modul reasoning sekaligus (Multibagger, Quality/Compound, Speculative). Format konkret bagaimana tiap modul "merespons" flag didetailkan di spec modul masing-masing (`07_MODULE_MULTIBAGGER.md`, `08_MODULE_QUALITY_COMPOUND.md`, `09_MODULE_SPECULATIVE.md`).

---

© AlphaForge v2
