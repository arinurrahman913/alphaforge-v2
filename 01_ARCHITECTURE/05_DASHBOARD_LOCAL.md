# Dashboard Lokal

**Status:** Aktif
**Doc version:** 3.0.0

---

## 1. Tujuan

Lapisan tampilan lokal untuk hasil Layer 1 dan Layer 2. Ada karena masalah yang dikeluhkan bukan kualitas analisanya, tapi **transparansinya**: hasil keluar, tapi tidak kelihatan angka dan alasan di baliknya.

Dashboard ini menjawab satu pertanyaan berulang: *"angka ini dari mana, dan kenapa kesimpulannya begitu?"*

Konsekuensinya, prinsip utama dashboard bukan keindahan, tapi **ketertelusuran**. Setiap angka yang tampil harus bisa ditelusuri ke sumber, waktu tarik, dan versi metodologinya — tanpa perlu buka kode.

## 2. Prinsip Dashboard

### 2.1 Dashboard Menampilkan, Tidak Menghitung

Dashboard **hanya membaca artefak yang sudah dihasilkan pipeline**. Tidak menghitung ulang apa pun, tidak memanggil provider apa pun, tidak menarik data live.

Ini bukan soal performa. Dashboard yang menghitung sendiri jadi **sumber kebenaran kedua** — dan begitu ada dua sumber kebenaran, angka di layar bisa berbeda dari angka yang tersimpan di Historical Tracking tanpa ada yang sadar. Yang diaudit nanti adalah yang tersimpan, bukan yang tampil. Keduanya wajib identik.

Aturan turunannya: kalau dashboard butuh sesuatu yang tidak ada di artefak, jawabannya adalah **menambah field di pipeline**, bukan menghitungnya di lapisan tampilan.

### 2.2 Narasi Adalah Artefak, Bukan Hasil Render

Ini konsekuensi 2.1 yang paling mudah terlewat.

Kalau narasi ("apa arti pembacaan ini") disusun saat halaman dibuka, maka narasi itu: tidak berversi, tidak tersimpan di Historical Tracking, tidak bisa diaudit, dan bisa berbeda tiap kali dashboard dibuka untuk data yang sama. Itu reasoning yang bersembunyi di lapisan tampilan — persis jenis ketidaktransparanan yang dashboard ini dibuat untuk mengatasi.

**Narasi dihasilkan pipeline, disimpan sebagai field, dan dashboard cuma merendernya.** Tiap narasi membawa `method_version`-nya sendiri.

### 2.3 Yang Kosong Harus Kelihatan Kosong

Field `missing` ditampilkan **eksplisit sebagai missing**, bukan disembunyikan, bukan diisi tanda hubung yang ambigu, bukan barisnya dihilangkan.

Alasannya sama dengan Prinsip #5: profil dengan 40 field lengkap dan profil dengan 25 field lengkap tidak boleh terlihat sama meyakinkan. Kalau baris yang kosong dihapus dari tampilan, dashboard justru **menyembunyikan** informasi paling penting tentang seberapa kuat kesimpulan itu berdiri.

Bedakan juga dua jenis kosong (lihat `03_LAYER2_SPECS/03_KNOWLEDGE.md` bagian 6):
- **`missing` karena data tidak berhasil ditarik** — masalah
- **`missing` karena tidak bermakna** (P/E perusahaan rugi) — normal dan informatif

Dua-duanya tampil, dengan penanda berbeda.

### 2.4 Lokal, Tanpa Server

Baca artefak dari disk, render statis. Tidak ada backend, tidak ada database eksternal, tidak ada state yang hidup di dashboard sendiri.

---

## 3. Halaman Layer 1 — Market Context

Sumber: `MarketContextPackage` (`04_DATA_CONTRACTS.md` §3), satu per sesi.

### Section 1 — Bukti Terkini & Analisa

Dua belas komponen, masing-masing satu kartu. Tiap kartu wajib menampilkan:

| Elemen | Isi | Dari field |
|---|---|---|
| Nama & nilai | Angka atau kategori | `name`, `value` |
| **Badge `kind`** | `DIRECT` / `DERIVED` — **wajib, visual, menonjol** | `kind` |
| Status | ok / degraded / missing | `status` |
| Narasi | Apa arti pembacaan ini | `narrative` (baru — lihat §5) |
| Sumber & waktu | Provider + timestamp tarik | `sources[]` |
| Versi metodologi | Hanya kalau `kind=derived` | `method_version` |
| Input | Komponen lain yang dipakai | `inputs[]` |
| Catatan | Kalau degraded/missing | `note` |

**Badge `kind` tidak boleh dikecilkan atau dijadikan tooltip.** Ini implementasi visual langsung dari Prinsip #5. Business Cycle Stage adalah pembacaan yang **kita konstruksi sendiri** dari tiga indikator FRED — ia tidak boleh tampil dengan bobot visual yang sama dengan VIX, yang satu angka resmi dari satu sumber. Kalau dua-duanya tampil sebagai angka rapi berdampingan tanpa pembeda, dashboard sedang berbohong secara tata letak.

**Urutan kartu:** ikuti DAG (`02_LAYER1_MARKET_CONTEXT.md` §5) — 11 komponen leaf dulu, `market_sentiment` terakhir. Urutan ini sekaligus mengajarkan strukturnya: yang terakhir adalah yang bergantung pada yang lain.

### Section 2 — Kesimpulan

Ringkasan kondisi market dari dua belas komponen.

> **⚠ Ini komponen baru yang belum ada spec-nya — lihat D-06.**
>
> "Kesimpulan Layer 1" bukan sekadar tampilan. Ia **sintesis** dari dua belas pembacaan, dan sintesis adalah reasoning. Artinya ia butuh: spec sendiri, `method_version`, `kind=derived`, dan confidence-nya sendiri. Kalau ia lahir di lapisan dashboard, ia jadi reasoning ketiga belas yang tidak berversi dan tidak tercatat — melanggar 2.1 dan 2.2 sekaligus.
>
> **Keputusan: `context_summary` dihasilkan Layer 1, bukan dashboard.** Dashboard cuma merendernya.

Batasan isi `context_summary` — ia **mendeskripsikan kondisi**, tidak merekomendasikan:

| Boleh | Tidak boleh |
|---|---|
| "Yield curve inverted, breadth menyempit, sentimen di zona greed" | "Saatnya defensif" |
| "3 dari 12 komponen degraded — pembacaan siklus lemah" | "Hindari saham growth" |
| "Konteks makro dan sentimen bertentangan arah" | "Skor market: 6.5/10" |

Larangan skor tunggal berlaku di sini persis seperti di Aggregator. "Skor market 6.5/10" adalah single-verdict versi makro — dan begitu ada, ia akan jadi satu-satunya hal yang dibaca orang dari dua belas komponen.

Section 2 wajib menampilkan **berapa komponen yang degraded/missing** secara menonjol. Kesimpulan yang berdiri di atas 9 dari 12 komponen tidak boleh terbaca sama kuatnya dengan yang berdiri di atas 12.

---

## 3b. Halaman Daftar — Masuk Lewat Lensa (D-08)

Sampai revisi ini dashboard tidak punya rute masuk. Ia menampilkan satu saham, sementara pipeline menghasilkan 1.500–3.000. Tidak pernah ada yang menjawab bagaimana sampai ke halaman itu.

**Tidak ada satu daftar. Ada tiga, plus satu.**

```
  ┌────────────────┬────────────────┬────────────────┬──────────────┐
  │  Multibagger   │ Quality/Comp.  │  Speculative   │  Divergensi  │
  │  urut menurut  │  urut menurut  │  urut menurut  │  urut menurut│
  │  kosakatanya   │  kosakatanya   │  kosakatanya   │  selisih     │
  │  sendiri       │  sendiri       │  sendiri       │  antar lensa │
  └────────┬───────┴────────┬───────┴────────┬───────┴──────┬───────┘
           └────────────────┴────────┬───────┴──────────────┘
                                     ▼
                   Halaman saham — TIGA lensa, termasuk
                   dua yang tadi tidak kamu pakai
```

Kamu memilih **pertanyaanmu** dulu, bukan sahamnya. Lalu di halaman saham kamu berhadapan dengan dua lensa yang tidak kamu pilih. Itu urutan yang benar: datang dengan pertanyaan, lalu diperlihatkan bahwa ada dua pertanyaan lain yang tidak kamu tanyakan.

Bandingkan dengan satu daftar berperingkat: kamu datang tanpa pertanyaan, sistem menyodorkan jawaban, dan tiga lensa jadi lampiran yang dibaca setelah keputusan sudah terbentuk. Itu v1 dengan langkah tambahan.

| # | Aturan mengikat |
|---|---|
| L1 | Tiga daftar **tidak pernah digabung** — tidak ada tampilan yang mencampur peringkat lintas lensa |
| L2 | **Tidak ada kolom lensa lain** di daftar lensa. Daftar Multibagger tidak boleh menampilkan stance Quality — itu penggabungan lewat pintu samping |
| L3 | Pengurutan memakai kosakata lensa itu sendiri (D-09), jadi lintas daftar tidak ada operasi banding |
| L4 | Halaman saham **selalu** menampilkan ketiganya, lewat daftar mana pun masuknya |
| L5 | Daftar Divergensi mengurut menurut jumlah & kedalaman divergensi, **tidak pernah** menurut stance |

**Daftar Divergensi** diurutkan dari `synthesis.surprise` (D-14) — bukan jumlah label berbeda. Terbukti perlu: PG dan MSFT sama-sama punya 3 label berbeda, tapi PG bisa ditebak penuh dari satu lensa (tidak informatif) sementara MSFT tidak bisa (sangat informatif). Daftar ini **cuma bisa dirender setelah populasi sesi lengkap** — beda dari tiga daftar per-lensa yang bisa dirender begitu ticker itu sendiri selesai Fase B.

Tiap baris daftar menampilkan: ticker, stance lensa itu, confidence, jumlah flag. **Tidak lebih.** Baris daftar bukan tempat meringkas — ia tempat memutuskan mau klik atau tidak.

## 4. Halaman Layer 2 — Analisa Saham

Sumber: `AggregatorOutput` (`04_DATA_CONTRACTS.md` §7) + `KnowledgeProfile` yang dirujuknya.

### Section 1 — Snapshot: Fakta Perusahaan

Seluruh tujuh bagian `KnowledgeProfile`, apa adanya:

| Bagian | Isi |
|---|---|
| 1 | Identitas — ticker, nama, sektor, industri, exchange |
| 2 | Kesehatan finansial — revenue, margin, balance sheet, cash flow |
| 3 | Posisi kompetitif — model bisnis, konsentrasi revenue, skala absolut |
| 4 | Tren historis — harga, volatilitas, earnings beat/miss |
| 5 | Kepemilikan — institusional, insider, transaksi signifikan |
| 6 | **Valuasi** — P/E, P/S, EV/EBITDA, P/B, FCF yield (absolut) |
| 7 | **Governance & peristiwa filing** — tren shares outstanding, auditor, restatement, litigasi |

Plus, di header section:

- **Kelengkapan field** — `field_completeness`: "38 dari 47 field terisi". Ditampilkan sebagai angka, bukan bar hijau yang menenangkan.
- **`evidence_snapshot_date`** — kapan faktanya diambil. Fakta enam minggu lalu dan fakta kemarin tidak boleh tampil sama.
- **Daftar sumber** — provider + waktu tarik.
- **`screening_flags`** — soft flag yang lolos dari Screening (ADR, IPO baru, dll).

Section 1 **tidak boleh mengandung kata sifat evaluatif**. "Margin 42%" boleh; "margin sehat" tidak. Itu bukan snapshot lagi, itu penilaian — dan penilaian adalah wewenang section 2. Aturan yang sama persis berlaku di `03_KNOWLEDGE.md`; dashboard tidak boleh menyelundupkannya kembali lewat label.

Angka relatif-peer (`PeerComparisonResult`) ditampilkan **terpisah dari bagian 2 dan 6**, dengan penanda jelas bahwa ini konteks peer, bukan fakta perusahaan — beserta `peer_group_size`, `low_sample_size`, dan `peer_failures` kalau ada. Ini menjaga batas D-03 tetap terlihat di layar, bukan cuma di spec.

### Section 2 — Hasil Reasoning & Kesimpulan

Tiga modul, **berdampingan, urutan tetap**: Multibagger → Quality/Compound → Speculative.

Tiap modul menampilkan seluruh isi `ModuleOutput`-nya:

| Elemen | Kenapa wajib tampil |
|---|---|
| `stance` + `stance_rationale` | Kesimpulan modul ini, dalam **kosakatanya sendiri** (D-09) — tidak sebanding dengan modul lain, dan memang tidak boleh dibandingkan |
| `confidence.score` + `band` + **`limiters`** | Limiters yang bikin skor confidence bermakna. Angka tanpa limiter cuma dekorasi. **Warnanya wajib netral** — lihat §4b |
| `flag_responses[]` | **Bukti Prinsip #4 dipatuhi.** Tiap red flag + bagaimana modul ini meresponsnya |
| `context_used[]` | **Ini jawaban langsung untuk "komponen mana yang benar-benar ngaruh"** — komponen Layer 1 mana yang mempengaruhi stance, dan bagaimana |
| `knowledge_gaps[]` | Field kosong yang membatasi kesimpulan ini |
| `method_version` | Formula versi berapa yang menghasilkan ini |

`risk_flags` ditampilkan **di atas ketiga kolom**, bukan di dalamnya — flag berlaku untuk sahamnya, bukan milik satu modul. Flag dengan `status=undetermined` diberi penanda berbeda dari `triggered`: "tidak ketemu masalah" dan "tidak sempat melihat" adalah dua hal berbeda, dan pembeda itu hilang kalau keduanya tampil sebagai kotak abu-abu yang sama.

`context_used` sebaiknya bisa diklik untuk melompat ke kartu komponen di halaman Layer 1. Ini yang menutup lingkaran transparansi: dari kesimpulan → ke komponen yang mempengaruhinya → ke sumber & waktu tariknya. Tiga klik, tanpa buka kode.

Kalau `halted=true`: tampilkan `halt_reason` dan `risk_flags`, ketiga kolom modul kosong. Jangan sembunyikan halamannya — saham yang dihentikan justru perlu terlihat.

### Kesimpulan: Peta Konvergensi (D-07)

Di **bawah** ketiga kolom — bukan di atas — tampil `synthesis`: peta di mana ketiga modul sepakat, di mana berbeda, dan **kenapa** berbeda.

Posisinya mengikat, bukan preferensi tata letak. Di atas, ia jadi verdict yang dibaca duluan dan tiga kolom berubah jadi lampiran. Di bawah, ia rangkuman setelah pembaca melihat sendiri. Posisi menentukan fungsi.

| Elemen | Isi |
|---|---|
| `agreements[]` | Klaim yang ketiganya searah, + modul mana + sitasi |
| `divergences[]` | Klaim yang berbeda, + posisi tiap modul, + **`root_cause`**, + sitasi |
| `narrative` | Rangkuman naratif |
| `confidence` | **Sama dengan confidence terendah dari tiga modul** — tampilkan modul mana sumbernya |
| `full_convergence` | Kalau `true`, tampilkan penanda: ketiga lensa sepakat penuh → patut dicurigai (tes T2, redefinisi D-14) |
| `surprise` | Angka mentah + persentil terhadap populasi sesi — ini yang menentukan urutan di daftar Divergensi |

**`root_cause` adalah inti gunanya.** "Multibagger dan Quality/Compound berbeda" belum informasi. Yang informasi: *kenapa* — beda field yang dibaca, beda bobot atas field yang sama, salah satu terhalang `knowledge_gap`, atau beda cara merespons flag yang sama.

Tiap sitasi bisa diklik ke field asalnya di Section 1 atau ke kolom modul di atasnya. Ini yang menutup rantai transparansi sepenuhnya: kesimpulan → sumber perbedaan → field → sumber data → waktu tarik.

**Yang dilarang tampil di sini:** buy/sell/hold, rekomendasi, skor apa pun, peringkat, "secara keseluruhan", dan skor kesepakatan ("3/3 modul positif" — itu skor yang menyamar jadi peta).

> **Uji pembeda, dipakai saat review tampilan:** kalau bagian ini bisa dibaca tanpa tiga kolom di atasnya dan tetap terasa cukup, ia sudah berubah jadi verdict dan D-07 perlu diperketat. `synthesis` yang benar justru **tidak berguna** sendirian — ia menunjuk ke atas.

### 4b. Confidence Tidak Boleh Diwarnai Seperti Kualitas

Aturan yang lahir dari kesalahan di mockup pertama: confidence 78 diberi bar cyan, confidence 54 diberi bar ochre. Cyan terbaca positif, ochre terbaca peringatan — sekali lihat, modul dengan confidence tertinggi terbaca sebagai pemenang.

Padahal **confidence mengukur kekuatan data, bukan daya tarik**. Modul bisa sangat yakin bahwa sesuatu itu buruk: `bukan_compounder` dengan confidence 88 adalah kesimpulan kuat, bukan kesimpulan bagus.

**Aturan:** bar dan angka confidence memakai **satu warna netral** di ketiga modul, tanpa gradasi semantik. Warna dipakai untuk `kind` (direct/derived), status (missing/degraded), dan flag — tidak untuk confidence, dan tidak untuk stance.

Stance juga tidak boleh diwarnai menurut "bagusnya", karena kosakata tiga modul tidak sebanding (D-09) — mewarnai `ruang_terbuka` hijau dan `ruang_tertutup` merah akan mengembalikan sumbu bersama lewat warna.

## 5. Yang Perlu Ditambahkan ke Pipeline

Dashboard ini butuh tiga hal yang **belum ada** di kontrak data. Semuanya dihasilkan pipeline, bukan dashboard (prinsip 2.1 & 2.2):

| Field | Di mana | Isi |
|---|---|---|
| `ComponentReading.narrative` | Layer 1, per komponen | Narasi arti pembacaan. Membawa `method_version` |
| `MarketContextPackage.context_summary` | Layer 1, per sesi | Kesimpulan Section 2 halaman Layer 1. `kind=derived`, punya `method_version` + confidence. Lihat D-06 |
| `ModuleOutput.stance_rationale` | Layer 2, per modul | Sudah ada di kontrak D-04 — pastikan terisi, bukan opsional |
| `AggregatorOutput.synthesis` | Layer 2, per saham | Peta konvergensi/divergensi (D-07). `kind` sintesis — punya `method_version` & confidence |
| `AggregatorOutput.catalysts` | Fase A, per ticker | `CatalystSet` (D-11) — sebelumnya dirujuk Speculative tanpa punya field di mana pun |
| `AggregatorOutput.confidence_report` | Fase B, per ticker | `ConfidenceReport` (D-11) — output komponen #5 yang selama ini tak pernah didefinisikan |

Perubahan ini menaikkan `04_DATA_CONTRACTS.md` ke versi 2.0.0.

---

## 6. Yang Masih Perlu Diputuskan

- **D-06** — spec `context_summary`: kriteria, ambang, apa yang membuatnya "degraded".
- **Modul reasoning: berbasis aturan atau LLM?** (D-10) Narasi sudah diputuskan deterministik — dirender dari field terstruktur, bukan dikarang. Tapi kalau modulnya sendiri LLM, T1a tetap tidak lulus. Bergantung pada isi kriteria modul, yang belum ada.
- **Riwayat sesi.** Dashboard menampilkan sesi terakhir saja, atau bisa telusuri sesi lama dari Historical Tracking? Yang kedua jauh lebih berguna untuk mengevaluasi reasoning, tapi butuh format penyimpanan yang sudah stabil.
- **Teknologi.** Static HTML dari template, atau app lokal. Belum diputuskan — tapi apa pun pilihannya, prinsip 2.1 mengikat.

---

## 7. Terhubung Dengan

Membaca: `MarketContextPackage` (Layer 1), `AggregatorOutput` + `KnowledgeProfile` + `PeerComparisonResult` (Layer 2), `HistoricalEntry` (opsional).

Tidak menulis apa pun. Tidak dibaca komponen lain — dashboard adalah ujung, bukan tahap.

---

© AlphaForge v2
