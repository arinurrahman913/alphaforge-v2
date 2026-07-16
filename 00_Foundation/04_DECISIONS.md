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
## D-08 — Masuk lewat satu lensa, bukan lewat daftar gabungan

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/05_DASHBOARD_LOCAL.md`, `01_ARCHITECTURE/03_LAYER2_STOCK_ANALYSIS.md`

### Masalah
Lubang terbesar di seluruh v2, dan tidak tersentuh oleh spec lama maupun D-01..D-07.

Pipeline menghasilkan 1.500–3.000 `AggregatorOutput`. Dashboard menampilkan satu saham. **Tidak pernah ada yang menjawab: bagaimana sampai ke halaman itu?** Tidak ada halaman daftar, tidak ada rute masuk.

Dan begitu daftar dibuat, ia butuh urutan. Urutan butuh peringkat. Peringkat dilarang D-04 & D-07.

Ini bukan kelalaian UI — ini **dua keputusan v2 yang saling bertabrakan**:

| | Populasi | Verdict | Bisa dibaca? |
|---|---|---|---|
| v1 | watchlist kecil | satu skor | ya |
| v2 | 8.000 saham | dilarang | **tidak** |

v2 membalik keduanya sekaligus. Tidak ada yang bisa membaca 2.000 laporan tiga lensa.

### Keputusan
**Larangannya adalah pada MENGGABUNG, bukan pada MENGURUTKAN.** Itu pembedaan yang selama ini tidak pernah dinyatakan, dan begitu dinyatakan, jalan keluarnya terbuka.

Prinsip #3 melarang tiga lensa dipadatkan jadi satu peringkat. Ia **tidak** melarang satu lensa mengurutkan menurut kriterianya sendiri — di dalam satu lensa, sumbu tunggal memang ada dan memang sah.

Maka: **tidak ada satu daftar. Ada tiga daftar, dan satu daftar keempat.**

```
  MASUK LEWAT LENSA — pilih pertanyaanmu dulu, bukan sahamnya

  ┌────────────────┬────────────────┬────────────────┐
  │  Multibagger   │ Quality/Comp.  │  Speculative   │
  │  ~2.000 saham  │  ~2.000 saham  │  ~2.000 saham  │
  │  urut menurut  │  urut menurut  │  urut menurut  │
  │  kriterianya   │  kriterianya   │  kriterianya   │
  │  SENDIRI       │  SENDIRI       │  SENDIRI       │
  └────────┬───────┴────────┬───────┴────────┬───────┘
           └────────────────┼────────────────┘
                            ▼
              Halaman saham — TIGA lensa, termasuk
              dua yang tadi tidak kamu pakai
```

**Daftar keempat — Divergensi.** Diurutkan menurut `synthesis.surprise` (D-14) — bukan jumlah label berbeda. Pembuktian D-13/D-14 pada PG vs MSFT menunjukkan kenapa: keduanya menghasilkan 3 label berbeda, tapi PG bisa ditebak penuh dari satu lensa (tidak informatif) sementara MSFT tidak bisa (sangat informatif). Jumlah divergensi tidak membedakan ini; `surprise` membedakannya. Ini peringkat **seberapa banyak yang bisa dipelajari**, bukan peringkat kualitas.

### Aturan
| # | Aturan |
|---|---|
| L1 | **Tiga daftar tidak pernah digabung.** Tidak ada tampilan yang mencampur peringkat lintas lensa |
| L2 | **Tidak ada kolom lensa lain di daftar lensa.** Daftar Multibagger tidak boleh menampilkan stance Quality — itu penggabungan lewat pintu samping |
| L3 | **Pengurutan di dalam daftar memakai kosakata lensa itu sendiri** (D-09), jadi lintas daftar tidak ada operasi banding |
| L4 | **Halaman saham selalu menampilkan ketiganya**, tak peduli lewat daftar mana masuknya |
| L5 | Daftar Divergensi mengurut menurut **`surprise`** (D-14) — **tidak pernah** menurut jumlah label berbeda atau menurut stance |

### Kenapa ini justru memperkuat Prinsip #3
Awalnya terdengar seperti kompromi — "ya sudah, kasih peringkat saja, asal per lensa". Bukan.

Masuk lewat satu lensa berarti kamu **menyatakan pertanyaanmu lebih dulu**. Lalu di halaman saham, kamu dipaksa berhadapan dengan dua lensa yang tidak kamu pilih. Itu urutan yang benar: kamu datang dengan pertanyaan, dan sistem menunjukkan bahwa ada dua pertanyaan lain yang tidak kamu tanyakan.

Bandingkan dengan satu daftar berperingkat: kamu datang tanpa pertanyaan, sistem menyodorkan jawaban, dan tiga lensa jadi lampiran yang dibaca setelah keputusan sudah terbentuk. Itu v1 dengan langkah tambahan.

### Alternatif yang ditolak
- **Urut menurut confidence** — mengurutkan menurut kekuatan data, bukan menurut apa pun yang kamu tanyakan. Saham dengan data lengkap dan tesis membosankan akan selalu di atas.
- **Satu daftar dengan tiga kolom stance** — inilah hitungan suara yang dilarang, cuma berbentuk tabel. Pembaca yang disuruh menjumlahkan.
- **Skor gabungan berbobot** — v1, persis.

### Biaya kalau dibalik
Sedang. Struktur daftar melekat ke dashboard dan ke cara orang memakai sistem — kebiasaan lebih mahal dibalik daripada kode.

---

## D-09 — Kosakata stance berbeda per modul

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/04_DATA_CONTRACTS.md`, `03_LAYER2_SPECS/07`, `08`, `09`

### Masalah
D-04 mendefinisikan `stance : enum{compelling, interesting, weak, not_applicable}` — **identik untuk ketiga modul**. Itu kesalahan saya sendiri, dan cukup serius.

Enum itu **ordinal**: compelling > interesting > weak. Dipakai identik oleh tiga modul, ia langsung jadi **hitungan suara**: "2 compelling, 1 weak". Verdict-nya tidak hilang — ia cuma pindah dari Aggregator ke kepala pembaca, dan justru jadi lebih sulit dilarang karena tidak ada field-nya.

Lebih dalam lagi, ia melanggar Prinsip #3 di akarnya. Kalau tiga lensa mengajukan **pertanyaan yang berbeda**, jawabannya tidak boleh berada di sumbu yang sama. `not_applicable`-nya Speculative dan `weak`-nya Multibagger tidak sebanding — tapi enum bersama menyatakan mereka sebanding.

### Keputusan
Tiap modul punya kosakata sendiri, diturunkan dari pertanyaannya sendiri:

| Modul | Pertanyaannya | Kosakata stance |
|---|---|---|
| **Multibagger** | Ada ruang untuk kelipatan besar? | `ruang_terbuka` · `ruang_sempit` · `ruang_tertutup` · `ruang_tak_terbaca` |
| **Quality/Compound** | Ini mesin compounding? | `compounding_kuat` · `compounding_rapuh` · `bukan_compounder` · `mesin_tak_terbaca` |
| **Speculative** | Ada asimetri berkatalis? | `asimetri_berkatalis` · `asimetri_tanpa_katalis` · `tanpa_asimetri` · `asimetri_tak_terbaca` |

**Tidak ada tabel pemetaan antar kosakata. Membuatnya dilarang** — itu akan mengembalikan sumbu bersama lewat pintu belakang.

### Kenapa ini bekerja
Suaranya tidak bisa dihitung bukan karena dilarang, tapi karena **operasinya tidak ada**. "`ruang_sempit` + `compounding_kuat` + `tanpa_asimetri`" tidak menjumlah jadi apa pun. Pembaca terpaksa membaca ketiganya sebagai tiga jawaban atas tiga pertanyaan — yang memang begitu adanya.

Di dalam satu kosakata, urutan tetap ada (`ruang_terbuka` > `ruang_sempit`), dan itu sah: satu lensa, satu sumbu. Itu yang membuat D-08 bisa mengurutkan daftar per lensa tanpa melanggar apa pun.

### Catatan: tiap modul punya keadaan "tak terbaca" sendiri
`not_applicable` yang lama merangkap dua hal: "modul ini tidak relevan untuk saham ini" dan "datanya tidak cukup untuk menjawab". Sekarang dipisah — keadaan `*_tak_terbaca` khusus untuk gap data, dan wajib menyebut `knowledge_gaps` penyebabnya (aturan V5 direvisi).

### Biaya kalau dibalik
Rendah di spec, sedang di kode kalau enum sudah tertanam.

---

## D-10 — Narasi dirender dari field terstruktur, bukan dikarang

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/04_DATA_CONTRACTS.md`, `01_ARCHITECTURE/05_DASHBOARD_LOCAL.md`

### Masalah
Dua hal yang saya tulis saling meniadakan:

- **T1 (D-04):** output harus **identik byte-per-byte** tiap dijalankan ulang.
- **Narasi (D-06):** "kalau LLM, `method_version` wajib mencakup versi model + prompt".

LLM tidak deterministik. `method_version` tidak bisa menangkap itu. Kalau narasi ditulis LLM, T1 tidak akan pernah lulus — dan tes yang tidak akan pernah lulus akan dimatikan orang dalam seminggu, lalu seluruh jaminan determinisme ikut mati bersamanya.

### Keputusan
**Narasi = render deterministik dari field terstruktur.** Bukan dikarang bebas.

Artinya urutannya dibalik: `agreements[]`, `divergences[]`, `root_cause`, `limiters` **dihitung dulu** oleh aturan. Prosanya template dari field-field itu.

### Alasan
Efek sampingnya justru yang paling berharga: **S2 (wajib sitasi) jadi terpenuhi secara struktural, bukan diawasi.** Narasi tidak bisa mengatakan apa pun yang tidak ada di field terstruktur — bukan karena dilarang, tapi karena tidak ada bahannya. Klaim tanpa sitasi jadi mustahil ditulis, bukan sekadar melanggar aturan.

Ini menutup celah terbesar yang tersisa di D-07: `synthesis` yang dikarang LLM bisa menyelundupkan penilaian yang tidak dimiliki modul manapun, dan tidak akan ada yang tahu.

### Harga yang dibayar
Prosanya lebih kaku dari contoh di mockup. Narasi template terbaca seperti template. Itu diterima — narasi di sini alat audit, bukan tulisan.

### T1 dipecah
Karena narasi kini turunan, T1 bisa lebih tajam:

- **T1a — determinisme struktural:** field terstruktur (`stance`, `flag_responses[].impact`, `context_used`, `root_cause`) harus identik tiap dijalankan ulang dengan input sama. **Gate keras.**
- **T1b — independensi urutan:** acak urutan eksekusi tiga modul → T1a tetap lulus.
- **Narasi tidak diuji terpisah** — kalau field-nya identik, narasinya identik dengan sendirinya.

### Kalau modul reasoning sendiri berbasis LLM
Maka T1a **tidak akan lulus**, dan itu keputusan yang harus diambil sadar — bukan ditemukan saat tes merah. Pilihannya: modul berbasis aturan (T1a jadi gate keras), atau modul berbasis LLM (T1a turun jadi pemantauan bertoleransi, dan `method_version` wajib memuat `model_version` + `prompt_version`).

**Belum diputuskan.** Tercatat di Keputusan Terbuka — dan ini bergantung pada isi kriteria modul, yang juga belum ada.

---

## D-11 — Catalyst Tracking & Confidence/Data Quality akhirnya punya kontrak

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/04_DATA_CONTRACTS.md`, `03_LAYER2_SPECS/05`, `10`

### Masalah
D-04 mengunci kontrak untuk semua paket — **kecuali dua ini**, dan tidak ada yang menyadarinya sampai audit.

- **Catalyst Tracking (#10)** ada di daftar file, ada di diagram, disebut Speculative. Tapi tidak ada di kontrak data, tidak ada di `AggregatorOutput`, tidak punya slot di Knowledge. Di mockup dashboard, Speculative menyatakan "tidak ada katalis dalam 90 hari" — **diambil dari field yang tidak ada.**
- **Confidence/Data Quality (#5)** punya komponen, tapi output-nya tidak pernah didefinisikan. Yang ada cuma `confidence` inline di `ModuleOutput`. Jadi #5 komponen atau bukan?

Ini persis kelas lubang yang ditemukan di review pertama (Knowledge tidak menopang konsumennya), diulangi oleh orang yang menemukannya.

### Keputusan

**Catalyst → `CatalystSet`, dihasilkan di Fase A, bukan bagian Knowledge.**

Katalis bukan fakta tentang keadaan perusahaan — ia fakta tentang kalender. Alasan utamanya **umur simpan**: `KnowledgeProfile` adalah potret pada `evidence_snapshot_date` dan seluruh isinya meluruh dengan laju yang sama. Katalis punya tanggal kedaluwarsa masing-masing — earnings 19 Juli mati pada 20 Juli, sementara margin 42% tidak. Menaruhnya di Knowledge berarti satu paket berisi field dengan laju luruh yang berbeda, dan itu akan salah dibaca cepat atau lambat.

**Confidence → `ConfidenceReport`, Fase B (butuh peer).**

Sekalian menjernihkan hubungan yang selama ini kabur:

- `ConfidenceReport` = seberapa kuat **data** saham ini. Satu per ticker.
- `ModuleOutput.confidence` = seberapa yakin **modul ini** pada kesimpulannya sendiri.
- **Aturan: `ModuleOutput.confidence.score` ≤ `ConfidenceReport.overall.score`.** Modul tidak boleh lebih yakin pada kesimpulannya daripada data yang menopangnya. Ini validasi baru: **V6**.

Tanpa V6, modul bisa mengaku yakin 90 di atas profil yang cuma 60% lengkap, dan tidak ada yang menghentikannya.

### Biaya kalau dibalik
Rendah untuk `ConfidenceReport`. Sedang untuk `CatalystSet` kalau sudah ada entri historis.

---

## D-12 — Pembatasan akses field per modul: yang menciptakan tiga lensa

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/04_DATA_CONTRACTS.md`, `03_LAYER2_SPECS/03`, `07`, `08`, `09`

### Masalah
D-09 memisahkan kosakata `stance` per modul. Tapi itu cuma menyembunyikan masalah yang lebih dalam: kalau ketiga modul membaca `KnowledgeProfile` yang **sama persis**, mereka cuma bisa berbeda soal **bobot** — bukan soal fakta. Kosakata beda, tapi bahan bakunya identik.

Tes lapangan (bukan tes 20 saham lengkap — cuma INTC, dijalankan manual) membuktikan ini bukan kekhawatiran teoretis. Kalau ketiga lensa boleh melihat GAAP EPS $(0.73) dan foundry loss $2.4B, ketiganya akan tertarik ke jawaban yang sama — angka sekeras itu menyeret semua lensa, berapa pun bobot yang dipasang.

### Keputusan
Tiap modul cuma boleh membaca **subset** `KnowledgeProfile`. Field di luar subsetnya tidak masuk `context_used`, tidak boleh mempengaruhi `stance`, dan pelanggarannya adalah bug — bukan pilihan implementasi.

| Bagian Knowledge | Multibagger | Quality/Compound | Speculative |
|---|:---:|:---:|:---:|
| 1 — Identitas | ✅ | ✅ | ✅ |
| 2 — Kesehatan finansial | **arah saja** | ✅ penuh | ❌ |
| 3a — Struktur (D-13) | ✅ | ✅ | ❌ |
| 3b — Momentum (D-13) | ✅ | ❌ | ❌ |
| 4 — Tren historis | ✅ | ✅ penuh | volatilitas saja |
| 5 — Kepemilikan | ❌ | ✅ | ✅ |
| 6 — Valuasi | ✅ | ✅ | ❌ |
| 7 — Governance | ❌ | ✅ | ❌ |
| `CatalystSet` | ✅ sekunder | ❌ | ✅ penuh |
| Peer | ✅ | ✅ | ❌ |

### Alasan
Ini bukan optimasi — **ini yang menciptakan lensanya.** Tanpa pembatasan, sistem tidak punya tiga lensa; ia punya satu lensa dengan tiga suara yang dipaksa mengucapkan kata berbeda lewat D-09. Setiap ❌ di tabel adalah klaim yang bisa salah dan wajib bisa dibela satu per satu — bukan default yang diam-diam disepakati. Pembelaan tiap baris ada di `03_LAYER2_SPECS/03_KNOWLEDGE.md` §akses dan di kriteria masing-masing modul (D-13).

Yang paling penting: **Speculative tidak boleh melihat valuasi (6).** Ini bukan gaya bicara — ini batas lensa. Asimetri berkatalis tidak butuh harga masuk yang wajar untuk jadi asimetri.

### Konsekuensi teruji
Kasus MSFT (D-13, §pembuktian): dengan pembatasan ini, Quality tidak pernah melihat Azure +40% (itu 3b) dan tetap sampai ke `compounding_rapuh` murni dari margin & capex. Tanpa pembatasan, Azure +40% kemungkinan akan menyeret Quality ke `compounding_kuat` — dan `synthesis` yang dihasilkan tidak akan pernah bisa membedakan "Quality salah baca" dari "Quality memang tidak boleh tahu".

### Biaya kalau dibalik
Tinggi setelah kriteria (D-13) ditulis di atasnya — keduanya saling bergantung.

---

## D-13 — Kriteria tiga modul: trajectory, bukan gap dan bukan momentum murni

**Status:** Aktif — draft komitmen pertama, belum diuji di luar 3 saham
**Menyentuh:** `03_LAYER2_SPECS/03`, `07`, `08`, `09`, `01_ARCHITECTURE/04_DATA_CONTRACTS.md`

### Masalah
Setelah D-01 sampai D-12, seluruh wadah sudah terkunci — kontrak, validasi, dashboard, akses field — tapi **isi kriteria tiga modul kosong**. Ini lubang yang paling menentukan v2 jadi apa, dan sengaja tidak diisi sampai ada bukti empiris untuk berdiri di atasnya, bukan tebakan dari kursi.

### Metode
Bukan diturunkan dari teori. Diekstrak dari perbandingan berpasangan: dipaksa memilih satu saham lewat satu lensa, sekali waktu, dan menulis alasannya dalam satu kalimat. Alasan-alasan itu diuji balik ke tiga kasus nyata (INTC, PG, MSFT — data Q1/Q3 FY2026) untuk melihat apakah kriterianya menghasilkan kesimpulan yang koheren atau berkontradiksi.

### Temuan yang menentukan bentuknya
Jawaban awal untuk Multibagger condong ke "ikuti arus yang lagi kuat" (Momentum) — tapi itu nyaris sama dengan pertanyaan Speculative, dan akan membuat dua modul redundan. Diselesaikan lewat **opsi ketiga**:

> **Multibagger = trajectory.** Bukan taruhan bahwa pasar belum tahu (Gap — nyaris mustahil dijadikan kriteria berbasis data tanpa jadi "saya lebih pintar dari pasar"). Bukan taruhan pada satu peristiwa (Momentum murni — itu Speculative). Melainkan: **kurva pertumbuhan yang sudah kelihatan sekarang, diteruskan 3–5 tahun, menghasilkan kelipatan dari basis sekarang — dan harga sekarang cuma mem-price tahun depan, bukan lima tahun ke depan.**

Pembeda bersih dari Speculative: **Speculative butuh tanggal resolusi. Multibagger tidak** — trajectory jalan terus dengan atau tanpa satu peristiwa tertentu.

Ini juga yang membelah bagian 3 Knowledge (lihat di bawah), dan yang membuat kasus INTC versi sebelumnya (external foundry $174M) ternyata **lebih lemah** dari kasus PLTR/TSM yang justru muncul dari intuisi pemilik produk — $174M dari basis nyaris nol belum trajectory yang kelihatan, baru opsi yang belum mulai.

### Keputusan: Bagian 3 Knowledge Dipecah

| | Isi | Multibagger | Quality |
|---|---|:---:|:---:|
| **3a — Struktur** | model bisnis, konsentrasi revenue, ukuran TAM, posisi kompetitif | ✅ | ✅ |
| **3b — Momentum** | pertumbuhan segmen, tren guidance vs konsensus, akselerasi/deselerasi | ✅ | ❌ |

Trajectory butuh dua bahan berbeda sifat: struktur (seberapa besar ruangnya) dan momentum (apakah sedang bergerak ke arah situ). Quality cuma butuh yang pertama — mesin yang awet tidak perlu sedang berakselerasi untuk dianggap awet. Kalau Quality boleh membaca momentum, ia akan pelan-pelan tergoda mengandalkannya dan menempel ke Multibagger — persis yang coba dihindari D-12.

### Kriteria — Draft Komitmen v1

**Quality/Compound** (baca 1,2,3a,4,5,6,7,peer — tidak baca 3b, `CatalystSet`):

| Stance | Syarat |
|---|---|
| `compounding_kuat` | Margin stabil/naik N periode · FCF positif berturut · konsentrasi revenue di bawah ambang · percentile margin peer di atas median · tanpa flag governance severity tinggi yang belum direspons |
| `compounding_rapuh` | Margin/FCF mulai turun, tapi 3a (model bisnis, posisi kompetitif) masih utuh |
| `bukan_compounder` | Kerugian struktural di bisnis inti, atau restatement, atau margin & FCF negatif bersamaan |
| `mesin_tak_terbaca` | Field kunci di 2/3a/7 `missing` melewati ambang |

**Multibagger** (baca 1,2-arah,3a,3b,4,6,peer,`CatalystSet`-sekunder — tidak baca 5,7):

| Stance | Syarat |
|---|---|
| `ruang_terbuka` | Pertumbuhan segmen (3b) berakselerasi/infleksi positif · valuasi sekarang cuma masuk akal dengan asumsi pertumbuhan segera melambat · TAM (3a) masih ada headroom (peer group nunjukin penetrasi/pangsa masih rendah) |
| `ruang_sempit` | Pertumbuhan kuat, tapi valuasi (peer percentile) sudah tinggi — kelanjutannya sudah mulai di-price |
| `ruang_tertutup` | Pertumbuhan datar/melambat dan TAM matang |
| `ruang_tak_terbaca` | 3b `missing`, atau peer group `low_sample_size` |

**Speculative** (baca 1, volatilitas-4, 5, `CatalystSet` penuh, makro — tidak baca 2,3,6):

| Stance | Syarat |
|---|---|
| `asimetri_berkatalis` | ≥1 katalis `scheduled`/`expected` dalam horizon · hasil genuinely tidak pasti (gap guidance-vs-konsensus, atau outcome biner) · volatilitas cukup |
| `asimetri_tanpa_katalis` | Tidak ada katalis dalam horizon, volatilitas tetap tinggi |
| `tanpa_asimetri` | Tidak ada katalis, volatilitas rendah |
| `asimetri_tak_terbaca` | `CatalystSet.status` degraded/missing |

### Pembuktian: Tiga Kasus Nyata (data Q1/Q3 FY2026)

| | Multibagger | Quality | Speculative | `root_cause` |
|---|---|---|---|---|
| **INTC** | `ruang_tak_terbaca`\* | `bukan_compounder` (GAAP −0.73, foundry loss $2.4B/kuartal, restructuring $4.1B) | `asimetri_berkatalis` (18A ramp, guidance $1.3B di atas konsensus) | `different_fields` |
| **PG** | `ruang_tertutup` (organic +3%, kategori mapan) | `compounding_kuat` (FCF productivity 82%, dividen 70 tahun) | `tanpa_asimetri` | `different_fields`, tapi ketiganya searah negatif-untuk-multibagger |
| **MSFT** | `ruang_sempit` (Azure +40%, tapi sudah di-price) | `compounding_rapuh` (GM 67.6% tersempit sejak 2022, capex naik 61% dengan $25M cuma inflasi komponen) | `asimetri_berkatalis` (restrukturisasi OpenAI, capex $3.4B di bawah konsensus) | `different_fields` |

\*Revisi dari klaim sebelumnya (`ruang_terbuka`) — di bawah kriteria trajectory, external foundry $174M dari basis nyaris nol bukan kurva yang sudah kelihatan.

**Temuan penting dari pembuktian ini, bukan cuma dari kriterianya:** PG dan MSFT sama-sama menghasilkan 3 label berbeda — skor "jumlah divergensi" identik. Tapi PG bisa ditebak sepenuhnya dari satu lensa (sepakat implisit); MSFT tidak bisa. **Jumlah label berbeda bukan ukuran informasi — yang ukuran informasi adalah seberapa tidak terduga kombinasinya dibanding populasi.** Ini melahirkan D-14.

### Yang Belum Diuji
- Ambang numerik (berapa persen margin turun → `compounding_rapuh`) — butuh lebih banyak saham, bukan keputusan dari kursi.
- Apakah pecahan 3a/3b ini benar, atau cuma salah satu cara membelah. Cara mengecek: prediksi treatment KO vs COST pakai kriteria ini, bandingkan dengan intuisi pemilik produk.
- Seluruh kriteria ini **draft komitmen pertama** — akan direvisi begitu dijalankan ke populasi penuh dan `surprise` (D-14) menunjukkan pola yang tidak konsisten dengan kriteria di atas.

### Kepemilikan
Kriteria ini punya dua penulis. Berbeda dari D-01 sampai D-11 yang murni keputusan arsitektur, isi tesis di dokumen ini adalah kolaborasi — dan karena itu **wajib** direvisi begitu pemilik produk merasa ada bagian yang "bukan ini yang dimaksud", bukan dipertahankan karena sudah tertulis rapi.

### Biaya kalau dibalik
Tinggi untuk struktur (bagian 3a/3b, akses field D-12) begitu kode dibangun di atasnya. Rendah untuk ambang numerik di dalam tiap kriteria — itu memang dirancang untuk diubah sering.

---

## D-14 — Daftar Divergensi diurutkan menurut *surprise*, bukan jumlah divergensi; T2 didefinisikan ulang

**Status:** Aktif
**Menyentuh:** `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6/§7, `01_ARCHITECTURE/05_DASHBOARD_LOCAL.md` §3b, D-04, D-08

### Masalah
D-08 aturan **L5** mengurutkan daftar Divergensi menurut "jumlah & kedalaman divergensi". Pembuktian D-13 pada PG dan MSFT membongkar ini: keduanya menghasilkan 3 label berbeda — skor identik menurut L5 — tapi PG **bisa ditebak penuh** dari satu lensa (consumer staples mapan → tiga lensa yang saling menentukan), sementara MSFT **tidak bisa**. Menurut L5, keduanya akan duduk sejajar di puncak daftar Divergensi. Itu salah: daftar itu akan dipenuhi saham paling membosankan di market, karena saham membosankan justru paling rapi menyapu ketiga sumbu dengan cara yang saling tertebak.

Masalah kedua, lebih diam-diam: D-04 tes **T2** ("kalau >90% ticker punya stance sama di ketiga modul, curigai Multi-Lens") berhenti bisa dihitung sejak D-09 memisahkan kosakata `stance` per modul. "Stance sama" tidak lagi punya arti ketika ketiga kosakata berbeda sumbu. Ini tidak disadari sampai sekarang.

### Keputusan
**Metrik yang benar: seberapa tidak terduga kombinasi stance ini, dibanding populasi sesi ini.**

```
P(stance_quality, stance_speculative | stance_multibagger)   ← dihitung dari seluruh populasi sesi (~1.500–3.000 ticker)

surprise(ticker) = −log P(kombinasi stance ticker ini | populasi sesi ini)
```

Daftar Divergensi (D-08) diurutkan menurut `surprise`, bukan jumlah label berbeda.

**T2 didefinisikan ulang:** bukan "berapa persen ticker punya stance sama", tapi **mutual information antar stance tiga lensa di seluruh populasi**. Kalau mendekati nol entropi bersyarat — lensa B bisa ditebak dari lensa A — lensa B redundan, Multi-Lens tinggal klaim di atas kertas.

### Alasan
Efek samping yang berharga: metrik ini **menemukan sendiri** kombinasi mana yang lumrah tanpa perlu ditetapkan manual, dan otomatis menyesuaikan tiap sesi — kalau seluruh market bergerak, standar "mengejutkan" ikut bergeser bersamanya.

T2 versi mutual information bisa dihitung otomatis, sekali per sesi, dari satu angka. **Ini artinya tesis inti v2 — apakah tiga lensa benar-benar independen — tidak perlu menunggu Historical Tracking v2.1 dan evaluasi bertahun-tahun.** Ia bisa dites tiap kali sesi selesai, dari populasinya sendiri.

### Konsekuensi
- `Synthesis` (D-07) mendapat field baru: `surprise` (angka) dan `population_baseline` (distribusi kombinasi stance saat itu dihitung).
- Perhitungan `surprise` **butuh populasi utuh** — memperberat barrier Fase B (D-03) satu langkah lagi: `synthesis` per ticker tidak bisa selesai sampai seluruh Knowledge→Peer→ModuleOutput populasi selesai lebih dulu.
- Daftar Divergensi (dashboard) tidak bisa dirender sebagian — ia menunggu sesi penuh, berbeda dari tiga daftar per-lensa yang bisa dirender begitu ticker itu sendiri selesai.

### Alternatif yang ditolak
- **Tetap jumlah divergensi, tambah pembobotan manual per kombinasi** — mengharuskan menetapkan "kombinasi mana yang lumrah" secara manual, dan tidak menyesuaikan otomatis kalau market bergeser.
- **Historical baseline lintas sesi** — lebih stabil secara statistik, tapi menunda metrik ini sampai ada riwayat sesi, padahal masalahnya perlu jawaban sekarang. Bisa jadi peningkatan v2.1 begitu Historical Tracking punya cukup data.

### Biaya kalau dibalik
Sedang. `surprise` field bisa dihapus dari kontrak tanpa merusak `agreements[]`/`divergences[]`; daftar Divergensi kembali ke L5 lama.

---


## Keputusan Terbuka (Belum Diputuskan)

Dicatat di sini supaya tidak keliru dianggap sudah beres:

- **Format output Confidence** — numerik 0–100, kategori diskrit, atau keduanya (`03_LAYER2_SPECS/05_CONFIDENCE_DATA_QUALITY.md`).
- **Ambang numerik di dalam kriteria D-13** — persen margin turun, lama pertumbuhan flat, dsb. Kerangkanya sudah ada, angkanya belum diuji ke populasi luas.
- **Apakah pecahan 3a/3b (D-13) sudah benar** — baru diuji ke 3 saham, bukan populasi.
- **Ambang kuantitatif red flag** — persentase dilusi, nilai insider selling, jangka waktu.
- **TTL cache per jenis data** dan ukuran batch aman untuk `yfinance`.
- **Budget waktu/call satu sesi penuh** — belum pernah dihitung. Lihat `04_DATA_SOURCES/05_RATE_LIMIT_CACHING_STRATEGY.md`.
- **Spec `context_summary`** — kriteria & ambang. Bentuknya mengikuti pola D-07: memetakan di mana 12 komponen searah dan di mana bertentangan, bukan skor market.
- **Modul reasoning: berbasis aturan atau LLM?** (D-10) Menentukan apakah T1a jadi gate keras atau pemantauan bertoleransi. Bergantung pada isi kriteria modul, yang belum ada.

---

© AlphaForge v2
