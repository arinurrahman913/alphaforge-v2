# Risk / Red-Flag Check

**Status:** Aktif — revisi: sumber data dikoreksi jadi Knowledge saja (D-01), tiap flag dipetakan ke field Knowledge, bentuk Flag dikunci
**Doc version:** 2.0.0

## Definisi

Pemeriksaan yang mendeteksi masalah serius pada suatu saham sebelum Knowledge diteruskan ke modul reasoning. Bukan gerbang yang menghentikan proses secara default — lihat bagian "Cara Kerja" untuk definisi operasional "gerbang" yang dipakai di sini.

## Kenapa Penting

Tanpa pemeriksaan eksplisit, risiko besar (kecurangan akuntansi, dilusi ekstrem, litigasi berat) bisa 'tenggelam' jadi salah satu dari banyak faktor yang dirata-rata — padahal seharusnya jadi peringatan yang tidak bisa diabaikan oleh modul reasoning manapun.

## Sumber Data / Pendekatan

Diturunkan **dari Knowledge saja** — tidak menjangkau Evidence mentah, tidak memanggil provider apa pun.

> **Koreksi (D-01):** versi sebelumnya menulis "Diturunkan dari Knowledge & Evidence", yang bertentangan langsung dengan aturan di `03_KNOWLEDGE.md` bahwa Risk beroperasi di atas Knowledge, bukan Evidence mentah. Penyebabnya bukan kalimatnya — tapi skemanya: field yang dibutuhkan Risk (tren shares outstanding, pergantian auditor, restatement, litigasi) memang belum punya rumah di Knowledge, jadi Risk terpaksa menjangkau ke belakang. Sejak Knowledge punya **bagian 7 — Governance & Peristiwa Filing**, lubang itu tertutup dan Risk cukup membaca Knowledge.

### Pemetaan Flag → Field Knowledge

Tiap pemeriksaan wajib menyebut field Knowledge yang dipakainya. Kalau sebuah flag tidak bisa dipetakan ke field yang ada, itu tanda skema Knowledge kurang — bukan izin untuk menjangkau Evidence.

| Pemeriksaan | Field Knowledge |
|---|---|
| Dilusi saham berlebihan | bagian 7 → tren shares outstanding (12 bulan) |
| Auditor berganti > 1x / 3 tahun | bagian 7 → riwayat pergantian auditor |
| Restatement 2 tahun terakhir | bagian 7 → riwayat restatement |
| Litigasi material berjalan | bagian 7 → litigasi tercatat di filing |
| Insider selling besar-besaran | bagian 5 → daftar transaksi insider signifikan |
| Fraud terkonfirmasi / delisting | bagian 7 → filing tidak biasa + bagian 1 → status instrumen |

### Data Governance yang `missing`

Kalau field bagian 7 berstatus `missing` (mis. filing tidak bisa ditarik), pemeriksaan terkait menghasilkan `status: undetermined` — **bukan** "tidak ada red flag". Bedanya penting: "tidak ketemu masalah" dan "tidak sempat melihat" adalah dua hal yang sangat berbeda, dan modul reasoning berhak tahu yang mana. `undetermined` wajib masuk ke `knowledge_gaps` di output modul (lihat `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6, aturan V4).

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

## Bentuk Flag

```
Flag {
  flag_id        : string        # stabil & unik, mis. "dilution_12m"
  category       : enum{accounting, governance, litigation, dilution, insider, listing_status}
  severity       : enum{tinggi, ekstrem}
  status         : enum{triggered, undetermined}
  knowledge_refs : [string]      # field Knowledge yang memicunya
  evidence_note  : string        # fakta pemicunya, bukan penilaian
  method_version : semver
}
```

`flag_id` harus **stabil lintas sesi** — inilah yang dirujuk `flag_responses[].flag_id` di output modul, dan yang dipakai audit historis untuk membandingkan flag yang sama antar waktu. Kalau `flag_id` berubah karena refactor, entri historis lama jadi tidak bisa dicocokkan.

## Cara Kerja

Jalankan serangkaian pemeriksaan pola red flag terhadap Knowledge, hasilkan flag dengan tingkat severity di atas.

**Untuk flag Severity Tinggi:** flag ditempelkan ke Knowledge sebagai data terstruktur yang terlihat oleh ketiga modul reasoning. "Wajib direspons" berarti tiap modul harus menyatakan secara eksplisit bagaimana flag ini memengaruhi kesimpulannya — lewat pembatasan confidence, catatan reasoning yang menyebutnya secara spesifik, atau penyesuaian penilaian. Modul tidak boleh menghasilkan kesimpulan yang diam-diam mengabaikan flag yang ada.

Sejak revisi ini, "wajib" itu **ditegakkan secara mekanis**, bukan diharapkan (lihat `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6):

- **V1** — output modul yang jumlah `flag_responses`-nya tidak cocok dengan jumlah flag severity tinggi **ditolak**.
- **V2** — rationale yang identik untuk dua flag berbeda ditandai `generic_response` untuk direview.

V2 ada karena V1 saja gampang dipenuhi dengan menyalin kalimat yang sama tiga kali — dan log progres 14 Juli sudah mencatat gejalanya duluan ("respons red-flag di 3 modul masih level umum, belum spesifik per jenis flag"). V1 memaksa modul menjawab; V2 memaksa jawabannya benar-benar berbeda.

**Untuk flag Severity Ekstrem:** proses berhenti di titik ini — saham tetap tercatat (dengan alasan berhenti tercantum), tapi tidak diteruskan ke tiga modul reasoning. Ini pengecualian eksplisit terhadap perilaku default "tidak menghentikan proses" di atas.

## Terhubung Dengan

Berlaku ke ketiga modul reasoning sekaligus (Multibagger, Quality/Compound, Speculative). Format konkret bagaimana tiap modul "merespons" flag didetailkan di spec modul masing-masing (`07_MODULE_MULTIBAGGER.md`, `08_MODULE_QUALITY_COMPOUND.md`, `09_MODULE_SPECULATIVE.md`).

---

© AlphaForge v2
