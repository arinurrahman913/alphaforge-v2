# AlphaForge v2 — Catatan Keputusan (Decision Log)

**Status:** Aktif
**Doc version:** 1.0.0

---

## Kenapa Dokumen Ini Ada

Prinsip #6 mensyaratkan sistem bisa diaudit ke belakang, termasuk mencatat versi metodologi. Tapi sampai revisi ini, spec sendiri tidak punya tempat untuk mencatat **kenapa** sebuah desain dipilih dan **apa yang ditolak**. Akibatnya keputusan lama tidak bisa dibedakan dari kebiasaan lama, dan siapa pun yang membacanya nanti (termasuk penulisnya sendiri) tidak tahu mana yang sudah dipertimbangkan matang dan mana yang cuma belum sempat dipikirkan.

Setiap entri di bawah mencatat: keputusan, alasan, alternatif yang ditolak, dan biaya kalau nanti dibalik.

Format ID: `D-<nomor>`. Entri tidak dihapus — kalau dibalik, statusnya diubah jadi `Dibatalkan oleh D-xx`, tidak dihilangkan.

---

## D-01 — Risk/Red-Flag Check hanya membaca Knowledge

**Status:** Aktif
**Menyentuh:** `03_LAYER2_SPECS/03_KNOWLEDGE.md`, `03_LAYER2_SPECS/04_RISK_REDFLAG_CHECK.md`

### Masalah
Dua dokumen saling bertentangan. `03_KNOWLEDGE.md` menutup dengan "Confidence, Peer, Risk — semua beroperasi di atas Knowledge, bukan Evidence mentah", sementara `04_RISK_REDFLAG_CHECK.md` menulis sumbernya "Diturunkan dari Knowledge & Evidence". Dua-duanya tidak bisa benar.

Akar masalahnya bukan kalimatnya, tapi skemanya: Risk butuh tren shares outstanding (dilusi), pergantian auditor, restatement, dan litigasi — dan tidak satupun punya field di Knowledge. Jadi Risk *terpaksa* menjangkau Evidence.

### Keputusan
Risk hanya membaca Knowledge. Knowledge diberi **bagian 7 — Governance & Peristiwa Filing** yang menampung field-field yang selama ini dijangkau diam-diam dari Evidence.

### Alasan
- Invariant "satu arah, tiap tahap cuma menerima paket dari tahap sebelumnya" (`01_ARCHITECTURE/01_SYSTEM_OVERVIEW.md` §2) adalah salah satu klaim struktural utama v2. Melonggarkannya untuk menambal ketidaksinkronan dokumen berarti membayar prinsip dengan harga kesalahan tulis — terbalik.
- Kalau Risk boleh nyelonong ke Evidence, tidak ada alasan prinsipil kenapa modul reasoning tidak boleh juga. Pengecualian pertama yang paling mahal.
- Efek samping yang menguntungkan: begitu field governance ada di Knowledge, Confidence/Data Quality bisa mengukur kelengkapannya (Prinsip #5). Selama field itu cuma hidup di Evidence, Confidence buta terhadapnya.

### Alternatif yang ditolak
Longgarkan aturannya — akui Risk memang baca Evidence, ubah satu kalimat di `03_KNOWLEDGE.md`. Lebih murah (1 baris vs 1 bagian skema baru), tapi menggugurkan klaim arsitektur demi kenyamanan, dan membuat Confidence permanen buta terhadap sinyal governance.

### Biaya kalau dibalik
Rendah. Hapus bagian 7 dari Knowledge, kembalikan sumber Risk jadi "Knowledge & Evidence", longgarkan kalimat penutup `03_KNOWLEDGE.md`. Tidak ada komponen lain yang bergantung pada bagian 7 selain Risk dan Confidence.

---

## D-02 — Valuasi jadi bagian tersendiri di Knowledge

**Status:** Aktif
**Menyentuh:** `03_LAYER2_SPECS/03_KNOWLEDGE.md`

### Masalah
Tabel "fakta turunan vs penilaian" di `03_KNOWLEDGE.md` mencontohkan `"P/E 34, median peer group 19"` sebagai contoh sah level Knowledge — tapi skema 5 bagiannya tidak punya slot valuasi sama sekali. Padahal Multibagger dan Quality/Compound dua-duanya mustahil bereasoning tanpa valuasi.

### Keputusan
Tambah **bagian 6 — Valuasi** sebagai bagian tersendiri (bukan diperluas ke dalam bagian 2).

### Alasan
- Valuasi bukan kesehatan finansial. Bagian 2 menjawab "kondisi perusahaannya bagaimana"; valuasi menjawab "harganya berapa relatif terhadap kondisi itu". Perusahaan sehat bisa mahal, perusahaan sakit bisa murah — mencampurnya membuat dua pertanyaan berbeda terlihat seperti satu.
- Bagian terpisah membuat Confidence bisa menandai "valuasi tidak bisa dihitung" (mis. perusahaan rugi, P/E tidak bermakna) tanpa mencemari skor kesehatan finansial.
- Nomor 6 dipilih supaya aditif — bagian 1–5 tidak bergeser nomornya, referensi lama tidak putus.

### Alternatif yang ditolak
Perluas bagian 2 (Kesehatan Finansial). Lebih ringkas, tapi menggabung dua pertanyaan yang sengaja dipisah v2 dari v1.

### Catatan penting
Valuasi di Knowledge **hanya angka absolut per ticker** (P/E, P/S, EV/EBITDA, FCF yield). Angka relatif terhadap peer bukan di sini — lihat D-03.

### Biaya kalau dibalik
Rendah. Pindahkan field ke bagian 2, hapus bagian 6.

---

## D-03 — Knowledge tetap murni per-ticker; Peer jalan di fase kedua

**Status:** Aktif
**Menyentuh:** `03_LAYER2_SPECS/03_KNOWLEDGE.md`, `03_LAYER2_SPECS/06_PEER_RELATIVE_COMPARISON.md`, `01_ARCHITECTURE/01_SYSTEM_OVERVIEW.md`, `01_ARCHITECTURE/03_LAYER2_STOCK_ANALYSIS.md`

### Masalah
Dua hal sekaligus:

1. **Melingkar.** Knowledge bagian 3 minta "pangsa revenue relatif terhadap total peer group" — jadi Knowledge butuh peer group. Tapi Peer/Relative Comparison beroperasi *di atas* Knowledge. A butuh B, B butuh A.
2. **Diagram tidak jujur soal bentuk pipeline.** Diagram menggambar `Knowledge → [Confidence + Peer] → Risk` seolah alur per-ticker yang lurus. Padahal Peer butuh Knowledge **saham lain** sesektor — mustahil jalan streaming per ticker. Ada barrier di situ yang tidak pernah digambar.

### Keputusan
- Field "pangsa revenue relatif terhadap total peer group" **dihapus dari Knowledge**. Knowledge murni diturunkan dari Evidence ticker itu sendiri, tanpa pengetahuan apapun tentang ticker lain.
- Pipeline Layer 2 diakui **dua fase** secara eksplisit, dengan barrier di antaranya:
  - **Fase A (per-ticker, paralel):** Evidence → Knowledge
  - **⟂ barrier:** tunggu Knowledge seluruh kandidat selesai
  - **Fase B (butuh populasi):** Peer/Relative Comparison → Confidence → Risk → 3 modul → Aggregator
- Semua angka relatif-peer hidup di output Peer, bukan di Knowledge.

### Alasan
- Menghapus satu field memutus lingkarannya sepenuhnya — tidak perlu akrobat urutan. Lingkaran itu ada *justru karena* satu field relatif nyelinap ke lapisan yang seharusnya absolut.
- Barrier-nya nyata dan tidak bisa dihindari selama Peer memakai populasi hasil screening sendiri. Menggambarnya lebih jujur daripada pura-pura linear lalu kaget saat implementasi.
- Sebagai bonus, batas Knowledge jadi jauh lebih tegas dan gampang dites: *Knowledge suatu ticker harus bisa dihitung dengan hanya Evidence ticker itu di tangan.* Itu satu baris test.

### Alternatif yang ditolak
- **Pindahkan Peer ke setelah Risk** — tidak menyelesaikan apa-apa; barrier-nya tetap ada, cuma pindah tempat.
- **Biarkan Knowledge memanggil Peer** — merusak invariant satu arah, sama seperti D-01.

### Biaya kalau dibalik
Sedang. Kalau field pangsa revenue dikembalikan ke Knowledge, lingkaran D-03 hidup lagi dan butuh solusi lain (mis. dua-pass Knowledge).

---

## D-04 — Output tiga modul reasoning punya kontrak formal

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/04_DATA_CONTRACTS.md` (baru), `03_LAYER2_SPECS/07`, `08`, `09`, `11`

### Masalah
Aggregator ditugasi "menampilkan tiga hasil berdampingan tanpa mengubah kesimpulan masing-masing". Tapi bentuk "hasil" itu tidak pernah didefinisikan di manapun. Berdampingan apanya, kalau bentuknya belum ada.

Turunannya: Prinsip #4 mewajibkan flag "direspons eksplisit", Prinsip #5 mewajibkan confidence menempel di tiap kesimpulan modul — dua kewajiban yang mengandaikan struktur output yang tidak pernah ditulis.

### Keputusan
Definisikan kontrak output modul di `01_ARCHITECTURE/04_DATA_CONTRACTS.md`, dengan dua aturan validasi yang **menolak output**, bukan sekadar mengharapkan:

- `flag_responses[]` harus punya satu entri untuk **setiap** flag severity tinggi yang menempel di Knowledge. Jumlah tidak cocok → output ditolak.
- `confidence` wajib ada dan wajib menyebutkan field `missing` yang membatasinya.

### Alasan
"Wajib direspons" tanpa mekanisme penolakan cuma harapan. Log progres 14 Juli sudah mencatat gejalanya sendiri: respons red-flag di tiga modul masih level umum. Prinsip yang tidak bisa gagal secara mekanis akan pelan-pelan jadi hiasan.

### Alternatif yang ditolak
Serahkan bentuk output ke fase implementasi. Itu yang sudah terjadi — dan hasilnya kode mendahului spec di bagian yang justru paling menentukan identitas v2.

### Biaya kalau dibalik
Tinggi. Aggregator, Confidence, dan Historical Tracking semuanya membaca kontrak ini.

---

## D-05 — Market Breadth dihitung atas universe sendiri, bukan S&P 500

**Status:** Aktif
**Menyentuh:** `02_LAYER1_SPECS/06_MARKET_BREADTH.md`, `04_DATA_SOURCES/01_PROVIDERS_OVERVIEW.md`

### Masalah
Spec Breadth minta "daftar konstituen index (S&P 500/NASDAQ)" dan log progres memperlakukannya seolah tinggal disuplai. Masalahnya daftar konstituen S&P 500 resmi itu berlisensi S&P Dow Jones Indices — yang gratis cuma scraping Wikipedia atau menebak dari holdings ETF. Jadi ini bukan "belum diberi input", ini sumber gratisnya memang tidak ada.

### Keputusan
Breadth dihitung atas **universe hasil Screening sendiri** (NASDAQ + NYSE dari listing files, setelah hard exclude).

### Alasan
- Datanya sudah ada dan sudah gratis. Tidak ada dependensi lisensi baru, tidak ada scraping baru.
- Lebih selaras dengan v2. Seluruh alasan Layer 2 menyaring market-wide adalah karena watchlist manual v1 membatasi apa yang bisa terlihat. Mengukur "kesehatan market" lewat 500 saham terbesar, lalu menganalisa saham di luar 500 itu, artinya konteksnya diukur dari populasi yang berbeda dari yang dianalisa.
- Ambang hard exclude Screening (market cap > $30jt, dollar volume > $300rb) sudah membuang shell dan ticker mati — persis noise yang bikin breadth mentah jadi tidak bermakna.

### Konsekuensi yang diterima
Angkanya **tidak bisa dibandingkan** dengan pembacaan breadth publik manapun ($ADD, % di atas MA200 versi S&P). Ini bukan bug — tapi harus dinyatakan di output supaya tidak salah dibaca sebagai indikator standar. Breadth atas ~2.000 saham hasil screening akan sistematis beda dari breadth atas 500 mega-cap, terutama saat rally sempit.

### Alternatif yang ditolak
- **Holdings ETF (SPY/QQQ)** — bisa, dipublikasikan issuer harian. Tapi menambah dependensi sumber baru untuk mendapat populasi yang justru kurang cocok dengan cakupan v2.
- **Scraping Wikipedia** — standar ganda dengan penolakan scraping Investing.com di `09_MACRO_CALENDAR.md`.

### Biaya kalau dibalik
Rendah, kalau nanti ada anggaran lisensi. Ganti sumber universe; rumus advance/decline dan % di atas MA200 tidak berubah.

---

## D-06 — Dashboard Lokal ditambahkan; `context_summary` milik Layer 1, bukan dashboard

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/05_DASHBOARD_LOCAL.md` (baru), `01_ARCHITECTURE/04_DATA_CONTRACTS.md`

### Masalah
Keluhan yang memicu ini bukan kualitas analisa, tapi transparansi: hasil keluar, tapi angka dan alasan di baliknya tidak kelihatan. Sampai revisi ini v2 tidak punya lapisan tampilan sama sekali — seluruh 37 dokumen berhenti di `AggregatorOutput`.

Menambah komponen bertabrakan dengan Prinsip #8 (kedalaman di atas pelebaran). Tapi kasus ini beda dari "menambah modul reasoning keempat": dashboard tidak menambah kemampuan analisa, ia membuat analisa yang **sudah ada** bisa diperiksa. Prinsip #8 melarang melebar sebelum yang ada matang — dan yang ada tidak bisa dimatangkan kalau hasilnya tidak bisa dilihat.

Turunannya: "Section 2 — Kesimpulan" di halaman Layer 1 terdengar seperti urusan tampilan, padahal bukan. Meringkas dua belas pembacaan jadi satu kesimpulan adalah **sintesis**, dan sintesis adalah reasoning.

### Keputusan
- Dashboard lokal ditambahkan sebagai lapisan tampilan, dengan aturan mengikat: **menampilkan, tidak menghitung**.
- **`context_summary` dihasilkan Layer 1**, bukan dashboard. Ia `kind=derived`, punya `method_version` dan confidence sendiri, dan ikut tersimpan ke Historical Tracking.
- **Narasi per komponen juga artefak pipeline**, bukan hasil render.

### Alasan
- Dashboard yang menghitung sendiri jadi sumber kebenaran kedua. Yang diaudit nanti adalah yang tersimpan, bukan yang tampil — begitu keduanya bisa berbeda, Prinsip #6 bocor tanpa ada yang sadar.
- Narasi yang lahir saat render tidak berversi, tidak tersimpan, dan bisa berbeda tiap kali halaman dibuka untuk data yang sama. Itu reasoning yang bersembunyi di lapisan tampilan — persis masalah yang dashboard ini dibuat untuk mengatasi.
- `context_summary` yang lahir di dashboard = komponen derived ketiga belas tanpa spec, tanpa versi, tanpa jejak audit.

### Alternatif yang ditolak
- **Dashboard menyusun narasi & kesimpulan sendiri saat render.** Jauh lebih cepat dibangun, dan godaannya nyata — tidak perlu ubah pipeline sama sekali. Ditolak karena memindahkan reasoning ke tempat yang tidak diaudit; ini persis bagaimana v1 kehilangan ketertelusurannya.
- **Tidak usah ada Section 2 di Layer 1** — cukup dua belas kartu, pembaca menyimpulkan sendiri. Konsisten secara prinsip, tapi menyerahkan beban sintesis dua belas komponen ke pembaca setiap kali; itu yang bikin konteks makro jarang benar-benar dipakai.

### Konsekuensi
`04_DATA_CONTRACTS.md` naik ke 1.1.0: `ComponentReading.narrative` dan `MarketContextPackage.context_summary` ditambahkan. Spec `context_summary` sendiri (kriteria, ambang, apa yang bikin degraded) **belum ditulis** — tercatat sebagai keputusan terbuka.

### Biaya kalau dibalik
Rendah untuk dashboard-nya (hapus satu dokumen, ia tidak dibaca komponen lain). Sedang untuk `context_summary` kalau sudah ada entri historis yang menyimpannya.

---

## D-07 — Section 2 Layer 2 menampilkan **peta konvergensi**, bukan verdict dan bukan tiga kolom telanjang

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/05_DASHBOARD_LOCAL.md` §4, `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §7

### Masalah
Deskripsi Section 2 berbunyi *"hasil analisa dari reasoning dan menampilkan kesimpulan"*. Awalnya ini dibaca sebagai pilihan antara dua opsi:

**(a)** Tiga `stance` berdampingan, pembaca menyimpulkan sendiri.
**(b)** Satu kesimpulan gabungan.

Keduanya ditolak. Yang menarik: **keduanya salah karena alasan yang berlawanan** — dan itu justru yang menunjukkan apa yang sebenarnya kurang.

### Kenapa (b) ditolak
Verdict gabungan melanggar Prinsip #3 dan D-04. Menaruhnya di dashboard tidak meringankan — justru memperberat: ia tetap single-verdict, tapi tanpa spec, tanpa confidence, tanpa `flag_responses`, dan tidak tersimpan ke Historical Tracking. Artinya tidak pernah bisa dievaluasi benar-salahnya. Itu melanggar Prinsip #3 di tempat yang paling sulit dideteksi nanti.

### Kenapa (a) juga ditolak
Ini bagian yang tidak terlihat di ronde sebelumnya.

Keluhan pemicu seluruh rombakan ini adalah **transparansi**. Tapi tiga kolom berdampingan tanpa apa-apa lagi bukan transparan — ia cuma **memindahkan beban sintesis ke pembaca, setiap kali, tanpa alat**. Pembaca yang melihat Multibagger bilang `weak` dan Quality/Compound bilang `compelling` tetap harus menebak sendiri kenapa keduanya berbeda: apakah karena membaca field yang berbeda? Karena bobot yang berbeda atas field yang sama? Karena salah satu kena `knowledge_gaps`?

Informasi itu **ada** di `ModuleOutput` — tersebar di `stance_rationale`, `context_used`, `knowledge_gaps`, `flag_responses` milik tiga modul. Tapi tersebar. Dan sesuatu yang harus dirakit ulang manual setiap kali secara praktis sama dengan tidak transparan.

Itulah kenapa permintaan "kesimpulan" muncul. Bukan karena ingin verdict — tapi karena tiga kolom saja memang belum selesai.

### Keputusan
**(c) — `synthesis`: peta konvergensi & divergensi.** Bukan menjawab *"jadi ini bagus atau nggak?"*, tapi menjawab *"ketiganya sepakat di mana, berbeda di mana, dan kenapa berbeda."*

Contoh bentuknya:

> Ketiga modul sepakat fundamental kuat (margin 42%, FCF positif 8 kuartal berturut). Perbedaannya di valuasi: Quality/Compound menilai P/E 34 wajar untuk margin sebesar itu; Multibagger menilai P/E 34 memangkas ruang naik sehingga stance-nya turun ke `weak`; Speculative menganggap valuasi tidak relevan tanpa katalis, dan tidak ada katalis dalam 90 hari. Tidak ada modul yang terhalang `knowledge_gaps`.

Perhatikan yang **tidak** ada di situ: tidak ada peringkat, tidak ada skor, tidak ada "sebaiknya". Yang ada: sumber perbedaannya bisa ditunjuk. Pembaca tetap yang memutuskan — tapi sekarang ia memutuskan sambil tahu di mana persisnya ketiga lensa berpisah.

### Kenapa ini menambah informasi, bukan mengurangi
Ini pembeda pokok dari (b), dan pantas dinyatakan tegas karena gampang kabur:

- **Verdict/skor memampatkan** tiga dimensi jadi satu, lalu membuang sisanya. Informasi hilang.
- **`synthesis` memetakan** — ia menunjuk balik ke tiga kolom, tidak menggantikannya. Ia daftar isi, bukan pengganti isi.

Uji pembeda: kalau `synthesis` bisa dibaca **tanpa** tiga kolom dan tetap terasa cukup, ia sudah jadi verdict dan sudah gagal. `synthesis` yang benar justru **tidak berguna** tanpa tiga kolom di atasnya — ia menunjuk ke sana.

### Aturan yang mengikat

| # | Aturan | Kenapa |
|---|---|---|
| S1 | **Ditampilkan DI BAWAH tiga kolom, tidak di atas.** Bukan preferensi tata letak — mengikat. | Di atas = verdict yang dibaca duluan, tiga kolom jadi lampiran. Di bawah = rangkuman setelah pembaca melihat sendiri. Posisi menentukan fungsi |
| S2 | **Wajib sitasi.** Tiap klaim menunjuk modul + field spesifik. Yang tidak bisa disitasi, tidak boleh ditulis | Mencegah `synthesis` menyelundupkan penilaian yang tidak dimiliki modul manapun |
| S3 | **Kosakata terlarang:** buy/sell/hold, rekomendasi, skor apa pun, peringkat, "secara keseluruhan", "overall" | Kata-kata ini pintu masuk verdict. Dilarang di level teks, bukan cuma niat |
| S4 | **Confidence `synthesis` = confidence TERENDAH dari tiga modul.** Bukan rata-rata, bukan dihitung sendiri | Rangkuman tidak boleh lebih yakin dari input terlemahnya. Rata-rata akan menyembunyikan satu modul yang buta |
| S5 | **Kalau ketiga stance sama**, `synthesis` wajib menyatakannya eksplisit dan memicu tanda T2 | Konvergensi penuh adalah sinyal Multi-Lens mungkin cuma klaim di atas kertas (D-04, tes T2). Justru saat paling nyaman itulah paling perlu dicurigai |
| S6 | Dihasilkan **pipeline**, masuk `AggregatorOutput`, tersimpan ke Historical Tracking, punya `method_version` | Sama seperti D-06. Reasoning yang lahir di lapisan tampilan tidak bisa diaudit |

### Alternatif yang ditolak
- **(a) tiga kolom telanjang** — konsisten secara prinsip, tapi menyerahkan perakitan ke pembaca setiap kali. Konsistensi yang dibayar dengan komponen yang tidak terpakai bukan konsistensi, itu penghindaran.
- **(b) verdict gabungan** — alasannya di atas.
- **Skor kesepakatan** ("3/3 modul positif") — terdengar seperti peta, sebenarnya skor. Memampatkan lagi, dan lebih licik karena menyamar sebagai statistik.

### Risiko yang diterima secara sadar
`synthesis` **tetap akan jadi yang paling sering dibaca**. Itu tidak bisa dihilangkan — teks naratif selalu menang melawan tabel. S1 (posisi di bawah) dan S2 (wajib sitasi) menekan risikonya, tidak menghapusnya.

**Yang harus diawasi setelah dipakai beberapa minggu:** kalau `synthesis` mulai bisa dibaca berdiri sendiri tanpa tiga kolom terasa hilang — itu tandanya ia sudah pelan-pelan berubah jadi verdict, dan D-07 perlu diperketat atau dicabut. Ini gejala yang kelihatan dari kebiasaan membaca sendiri, bukan dari kode.

### Biaya kalau dibalik
Rendah. Hapus field `synthesis` dari `AggregatorOutput` dan section-nya dari dashboard — tiga kolom tetap berdiri sendiri, kembali ke (a).

---

## Keputusan Terbuka (Belum Diputuskan)

Dicatat di sini supaya tidak keliru dianggap sudah beres:

- **Format output Confidence** — numerik 0–100, kategori diskrit, atau keduanya (`03_LAYER2_SPECS/05_CONFIDENCE_DATA_QUALITY.md`).
- **Bobot & kriteria tiga modul reasoning** — bentuk output-nya sudah dikunci (D-04), isi reasoning-nya belum.
- **Ambang kuantitatif red flag** — persentase dilusi, nilai insider selling, jangka waktu.
- **TTL cache per jenis data** dan ukuran batch aman untuk `yfinance`.
- **Budget waktu/call satu sesi penuh** — belum pernah dihitung. Lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.
- **Spec `context_summary`** — kriteria & ambang. Bentuknya mengikuti pola D-07: memetakan di mana 12 komponen searah dan di mana bertentangan, bukan skor market.
- **Siapa penulis narasi** — kalau LLM, `method_version`-nya wajib mencakup versi model + prompt.

---

© AlphaForge v2
