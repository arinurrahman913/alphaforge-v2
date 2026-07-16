# Changelog — AlphaForge v2 (Spec)

Format: perubahan dicatat per revisi, mengacu ke entri `D-xx` di `00_Foundation/04_DECISIONS.md`.

---

## 2026-07-16 (lanjutan) — Kriteria Modul & Akses Field (D-12, D-13, D-14)

Titik paling menentukan sejak proyek dimulai: kriteria tiga modul reasoning, yang sejak review pertama sengaja dibiarkan kosong menunggu bukti, sekarang punya draft komitmen pertama.

### D-12 — Pembatasan akses field per modul
Tiap modul reasoning cuma boleh membaca subset `KnowledgeProfile`, bukan keseluruhannya. **Ini bukan optimasi — ini yang menciptakan tiga lensa.** Tanpa pembatasan, ketiga modul membaca fakta identik dan cuma bisa berbeda soal bobot (D-09 jadi kosakata kosong tanpa isi berbeda di baliknya).

### D-13 — Kriteria & tesis tiga modul
- **Multibagger = trajectory** — bukan Gap (pasar belum tahu, nyaris mustahil dijadikan kriteria data) dan bukan Momentum murni (itu Speculative). Kurva yang sudah kelihatan, diteruskan 3–5 tahun, menghasilkan kelipatan; harga sekarang cuma mem-price tahun depan.
- **Quality = kelanjutan** — mesin ini jalan, akan terus jalan atau tidak. Sengaja **tidak boleh membaca momentum (3b)** atau `CatalystSet`, supaya tidak tergoda menempel ke tesis Multibagger.
- **Speculative = resolusi katalis** — butuh tanggal. Sengaja **tidak boleh membaca valuasi (6)** — asimetri tidak butuh harga masuk wajar untuk jadi asimetri.
- **Bagian 3 Knowledge dipecah 3a (struktur) / 3b (momentum).** Trajectory butuh keduanya; kelanjutan cuma butuh yang pertama.
- **Diuji ke 3 kasus nyata** (data Q1/Q3 FY2026): INTC → `bukan_compounder` + `asimetri_berkatalis` + `ruang_tak_terbaca` (revisi dari klaim awal `ruang_terbuka`). PG → `compounding_kuat` + `tanpa_asimetri` + `ruang_tertutup`. **MSFT → `compounding_rapuh`** (GM tersempit sejak 2022, capex naik 61% dengan sebagian cuma inflasi komponen) **dicapai tanpa pernah melihat Azure +40%** (3b) — bukti langsung D-12 menghasilkan kesimpulan berbeda dari kalau semua field dibuka.
- Draft ini **punya dua penulis** dan wajib direvisi kalau pemilik produk merasa ada bagian yang salah — bukan dipertahankan karena sudah tertulis rapi.

### D-14 — `surprise` menggantikan jumlah divergensi; T2 didefinisikan ulang
Pembuktian D-13 membongkar metrik daftar Divergensi (D-08 L5): PG dan MSFT sama-sama 3 label berbeda — skor identik menurut "jumlah divergensi" — tapi PG bisa ditebak penuh dari satu lensa (tidak informatif), MSFT tidak bisa (sangat informatif). **Jumlah label berbeda bukan ukuran informasi.**

Penggantinya: `surprise(ticker) = −log P(kombinasi stance | populasi sesi ini)`, dihitung dari seluruh populasi sesi. Efek samping: **T2 (D-04) yang sudah tidak bisa dihitung sejak D-09** (kosakata stance terpisah membuat "stance sama" kehilangan arti) sekarang didefinisikan ulang lewat mutual information antar lensa — bisa dihitung otomatis tiap sesi, tidak perlu menunggu Historical Tracking v2.1.

### Konsekuensi teknis
- Kontrak data → 3.0.0: `Synthesis.surprise`, `Synthesis.population_baseline`.
- `KnowledgeProfile` → 3.0.0: bagian 3 pecah 3a/3b, tabel akses field per modul.
- Barrier Fase B (D-03) diperberat satu langkah: `surprise` butuh populasi utuh, tidak bisa dihitung per-ticker.

---

## 2026-07-16 — Dashboard Lokal (D-06, D-07)

Rombakan lapisan tampilan. Pemicunya: transparansi — hasil keluar, tapi angka & alasan di baliknya tidak kelihatan.

### Baru
- **`01_ARCHITECTURE/05_DASHBOARD_LOCAL.md`** — dashboard lokal, 2 halaman × 2 section:
  - **Layer 1** — §1: 12 kartu komponen (nilai + badge `kind` + narasi + sumber + waktu tarik + `method_version`), urut mengikuti DAG. §2: kesimpulan konteks market.
  - **Layer 2** — §1: snapshot 7 bagian Knowledge + kelengkapan field + tanggal snapshot + sumber. §2: tiga modul berdampingan (stance, confidence + limiters, `flag_responses`, `context_used`, `knowledge_gaps`).
- **D-06** — dashboard ditambahkan; `context_summary` & narasi per komponen **dihasilkan Layer 1, bukan dashboard**.
- **D-07** — **diputuskan**: §2 Layer 2 menampilkan `synthesis` — peta konvergensi & divergensi, bukan verdict dan bukan tiga kolom telanjang.

### Aturan mengikat dashboard
- **Menampilkan, tidak menghitung.** Baca artefak, jangan hitung ulang, jangan panggil provider. Dashboard yang menghitung sendiri = sumber kebenaran kedua; yang diaudit nanti adalah yang tersimpan, bukan yang tampil.
- **Narasi adalah artefak, bukan hasil render.** Narasi yang lahir saat halaman dibuka tidak berversi dan bisa beda tiap kali dibuka untuk data yang sama.
- **Yang kosong harus kelihatan kosong.** Field `missing` tampil eksplisit, dengan pembeda antara "gagal ditarik" vs "tidak bermakna".
- **Badge `kind` menonjol.** Business Cycle Stage (derived) tidak boleh tampil sebobot VIX (direct).
- **Section 1 Layer 2 tanpa kata sifat evaluatif.** "Margin 42%" boleh, "margin sehat" tidak.

### D-07 — kenapa (a) dan (b) dua-duanya ditolak
- **(b) verdict gabungan** melanggar Prinsip #3 & D-04 — dan di dashboard justru lebih buruk: tetap single-verdict, tapi tanpa confidence dan tidak tersimpan ke Historical Tracking, jadi tidak pernah bisa dievaluasi benar-salahnya.
- **(a) tiga kolom telanjang** juga ditolak. Ini yang tidak terlihat di ronde sebelumnya: tiga kolom tanpa apa-apa lagi bukan transparan — ia memindahkan beban sintesis ke pembaca setiap kali, tanpa alat. Informasi *kenapa* dua modul berbeda memang ada, tapi tersebar di `stance_rationale` + `context_used` + `knowledge_gaps` milik tiga modul. Yang harus dirakit manual tiap kali = praktis tidak transparan. Itulah kenapa permintaan "kesimpulan" muncul.
- **(c) `synthesis`** — memetakan, tidak memampatkan. Aturan: wajib sitasi (S2), confidence = terendah dari 3 modul bukan rata-rata (S4), posisi di bawah tiga kolom (S1), kosakata verdict dilarang (S3), konvergensi penuh picu tes T2 (S5).
- **Uji pembeda:** kalau `synthesis` bisa dibaca tanpa tiga kolom dan terasa cukup, ia sudah jadi verdict dan gagal.

### Kontrak data → 1.2.0
- `ComponentReading.narrative` + `narrative_version`
- `MarketContextPackage.context_summary` → `ContextSummary {kind, method_version, narrative, confidence, components_degraded}`
- `AggregatorOutput.synthesis` → `Synthesis {agreements[], divergences[] (+root_cause), narrative, confidence, full_convergence}`
- `ContextSummary` & `Synthesis` **dilarang** punya skor tunggal — termasuk skor kesepakatan ("3/3 modul positif"), yang terdengar seperti peta tapi sebenarnya skor

### Catatan Prinsip #8
Dashboard menambah komponen, dan Prinsip #8 melarang melebar sebelum yang ada matang. Kasus ini diterima karena dashboard tidak menambah kemampuan analisa — ia membuat analisa yang sudah ada bisa diperiksa. Yang ada tidak bisa dimatangkan kalau hasilnya tidak bisa dilihat.

---

## 2026-07-15 — Revisi Konsistensi Struktural

Revisi ini menutup lima lubang struktural yang ditemukan saat review menyeluruh 37 dokumen. Tidak ada komponen baru yang ditambahkan (Prinsip #8: kedalaman di atas pelebaran) — yang bertambah cuma dua dokumen fondasi (`04_DECISIONS.md`, `04_DATA_CONTRACTS.md`) yang mengunci hal-hal yang sebelumnya menggantung.

### Kontradiksi yang diperbaiki

- **Risk/Red-Flag Check** menyebut sumbernya "Knowledge & Evidence", padahal `03_KNOWLEDGE.md` menyatakan Risk beroperasi di atas Knowledge saja. Akarnya: field yang dibutuhkan Risk (dilusi, auditor, restatement, litigasi) tidak punya rumah di Knowledge. → **Knowledge bagian 7 (Governance & Peristiwa Filing)** ditambahkan; sumber Risk dikoreksi jadi Knowledge saja. (**D-01**)
- **Valuasi** dicontohkan sebagai fakta level Knowledge di tabel `03_KNOWLEDGE.md`, tapi tidak punya slot di skema 5 bagiannya — padahal Multibagger & Quality/Compound dua-duanya butuh. → **Knowledge bagian 6 (Valuasi)** ditambahkan. (**D-02**)
- **Knowledge ↔ Peer melingkar.** Knowledge bagian 3 minta "pangsa revenue relatif terhadap total peer group", padahal Peer beroperasi di atas Knowledge. → field dikeluarkan dari Knowledge; angka relatif-peer sekarang hanya hidup di output Peer. (**D-03**)

### Yang sebelumnya tidak pernah didefinisikan

- **Kontrak data** (`01_ARCHITECTURE/04_DATA_CONTRACTS.md`, baru) — bentuk formal Market Context Package, Knowledge Profile, Peer Result, **Module Output**, Aggregator Output, Historical Entry. Sebelumnya cuma Knowledge yang punya skema; Aggregator ditugasi "menampilkan tiga hasil berdampingan" tanpa bentuk hasil itu pernah ditulis. (**D-04**)
- **Aturan validasi V1–V5** — "flag wajib direspons" (Prinsip #4) dan "confidence eksplisit" (Prinsip #5) sekarang ditegakkan secara mekanis: output modul **ditolak** kalau `flag_responses` tidak lengkap atau `limiters` kosong saat confidence rendah. V2 menangkap rationale generik yang disalin antar flag — gejala yang sudah tercatat di log progres 14 Juli. (**D-04**)
- **Tes independensi T1 & T2** — Prinsip #3 sudah mendefinisikan "independen" secara teknis tapi tidak pernah menyatakan cara mengeceknya.
- **Pipeline dua fase + barrier** — diagram lama menggambar Layer 2 sebagai alur lurus per-ticker; kenyataannya Peer butuh populasi utuh, jadi ada barrier setelah Knowledge. Sekarang digambar eksplisit. (**D-03**)
- **DAG Layer 1** — frasa "saling melengkapi" di bagian *Dipakai Oleh* ternyata menggambarkan kedekatan tema, bukan aliran data. Setelah ditelusuri: **11 dari 12 komponen adalah leaf** dan bisa paralel penuh; cuma `market_sentiment` yang punya dependensi nyata (VIX + Market Breadth). Layer 1 jauh lebih murah dari yang tersirat sebelumnya.
- **Konvensi versi** — `Doc version`, `Method version`, `Kind` di header tiap spec. Prinsip #6 mensyaratkan versi metodologi dicatat di tiap entri historis, tapi spec sendiri belum berversi.
- **Decision log** (`00_Foundation/04_DECISIONS.md`, baru) — keputusan + alternatif yang ditolak + biaya kalau dibalik.

### Asumsi yang dikoreksi

- **Market Breadth** — daftar konstituen S&P 500 diperlakukan seolah tinggal disuplai. Kenyataannya daftar resmi itu berlisensi S&P DJI; tidak ada sumber gratis-resmi. → breadth dihitung atas **universe hasil Screening sendiri**, yang gratis, sudah ada, dan lebih konsisten dengan cakupan market-wide v2. Konsekuensinya (angka tidak sebanding dengan breadth publik) dinyatakan eksplisit. (**D-05**)
- **Historical Tracking** — pengecualian v2.1 sebelumnya terbaca sebagai "seluruh komponen ditunda". → dipisah: **penyimpanan** snapshot jalan sejak v2.0 (murah), **evaluasi** yang menyusul ke v2.1. Kalau penyimpanan ikut ditunda, saat v2.1 datang tidak ada data untuk dievaluasi.
- **Standar scraping** — `09_MACRO_CALENDAR.md` menolak scraping Investing.com sementara seluruh sistem berdiri di atas `yfinance` yang juga scraping. Aturannya sekarang tertulis: kalau ada sumber resmi gratis yang setara, pakai itu; kalau tidak ada, scraping lewat library mapan diterima sebagai risiko sadar. AAII dicatat jujur statusnya.
- **README** — masih menyatakan "implementasi kode belum dimulai", padahal log progres hari yang sama mencatat kode sudah end-to-end + 18 test + validasi live.

### Yang sengaja TIDAK diubah

- Kriteria & bobot tiga modul reasoning. Wadahnya dikunci, isinya tetap keputusan terbuka — ini yang paling menentukan identitas v2 dan tidak pantas diputuskan sambil lalu.
- Ambang kuantitatif red flag, format output Confidence, TTL cache, budget waktu/call satu sesi penuh. Semua tercatat di bagian "Keputusan Terbuka" di `04_DECISIONS.md`.

---

© AlphaForge v2
