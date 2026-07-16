# Aggregator & Output

**Status:** Aktif — revisi: kontrak output, urutan tampilan ditetapkan, larangan field verdict (D-04)
**Doc version:** 2.0.0

## Definisi

Komponen akhir Layer 2 yang menyusun hasil dari ketiga modul reasoning (Multibagger, Quality/Compound, Speculative) untuk ditampilkan berdampingan.

## Kenapa Penting

Ini adalah implementasi langsung dari Prinsip #3 (Multi-Lens, Bukan Single-Verdict) — Aggregator sengaja tidak diberi wewenang menggabungkan tiga pandangan jadi satu angka.

## Sumber Data / Pendekatan

Menerima `ModuleOutput` dari ketiga modul reasoning, beserta Confidence Score dan flag dari Risk/Red-Flag Check.

## Cara Kerja

Susun ketiga hasil reasoning berdampingan tanpa mengubah kesimpulan masing-masing modul. Sertakan Confidence Score dan Risk flag secara eksplisit di output, bukan disembunyikan.

Bentuk paketnya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §7 (`AggregatorOutput`).

## Urutan Tampilan

`module_outputs` **selalu** berurutan tetap: `multibagger`, `quality_compound`, `speculative`.

Ini keputusan sadar, bukan default yang kebetulan. Alternatifnya — mengurutkan berdasarkan `confidence` atau `stance` — terdengar netral tapi justru menyelundupkan peringkat lewat pintu belakang: modul yang tampil paling atas otomatis terbaca sebagai "jawabannya". Itu single-verdict yang menyamar jadi tata letak, dan seluruh Prinsip #3 dibuat untuk mencegahnya.

Urutan tetap tetap punya bias anchoring — yang pertama dibaca duluan, dan Multibagger akan selalu dapat perhatian pertama. Bedanya: bias itu **konstan**, sama untuk semua saham, jadi tidak bisa disalahartikan sebagai sinyal tentang saham ini. Bias yang konstan bisa dikenali dan dikompensasi pembaca. Bias yang berubah-ubah per saham tidak bisa — ia terlihat seperti informasi.

## Yang Tidak Boleh Ada di Output

`AggregatorOutput` **dilarang** punya field `verdict`, `score`, `rank`, `recommendation`, atau turunan apa pun yang meringkas tiga pandangan jadi satu.

Ini bukan "belum sempat" — ini tugas yang sengaja tidak diberikan (`01_ARCHITECTURE/01_SYSTEM_OVERVIEW.md` §3: *"Aggregator tidak menyimpulkan"*). Ditulis sebagai larangan eksplisit karena inilah tekanan yang paling mudah menyerah di fase implementasi: satu angka ringkasan selalu terasa lebih enak dipakai daripada tiga pandangan yang harus dibaca. Persis itu yang bikin v1 gagal.

Kalau suatu saat ini dilanggar, lakukan lewat entri baru di `00_Foundation/04_DECISIONS.md` yang membatalkan D-04 secara eksplisit — bukan diam-diam saat implementasi.

## Kalau Proses Dihentikan

Saham yang berhenti di Risk severity ekstrem (fraud terbukti, delisting) tetap menghasilkan `AggregatorOutput` dengan `halted=true`, `module_outputs` kosong, dan `halt_reason` terisi. `risk_flags` tetap lengkap.

Entri ini **tetap disimpan** ke Historical Tracking. Saham yang dihentikan justru penting untuk diaudit — kalau ternyata sistem sering menghentikan saham yang kemudian baik-baik saja, ambang severity ekstrem-nya yang salah.

## Terhubung Dengan

Menerima input dari ketiga modul reasoning (`ModuleOutput`), Confidence/Data Quality, Risk/Red-Flag Check. Outputnya diteruskan ke Historical Tracking / Decision Journal.

---

© AlphaForge v2
