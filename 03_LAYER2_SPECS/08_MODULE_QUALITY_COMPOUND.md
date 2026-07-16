# Module — Quality / Compound

**Status:** Aktif — revisi: kontrak output & aturan validasi flag ditambahkan (D-04)
**Doc version:** 3.1.0

## Definisi

Salah satu dari tiga modul reasoning independen. Fokus pada saham berkualitas tinggi dengan moat kuat dan kinerja konsisten dari waktu ke waktu.

## Kenapa Penting

Gaya compounder (ala Buffett/Terry Smith) menilai ketahanan dan konsistensi jangka panjang — kriteria ini seringkali bertentangan dengan kriteria pertumbuhan eksplosif ala Multibagger, sehingga perlu modul terpisah.

## Sumber Data / Pendekatan

Knowledge + Market Context Package, terutama Yield Curve dan Liquidity Conditions sebagai konteks biaya modal & valuasi jangka panjang. Juga memakai hasil Peer/Relative Comparison.

## Cara Kerja

Reasoning modul ini menilai: kekuatan moat kompetitif, konsistensi ROE/margin, kualitas balance sheet, rekam jejak alokasi modal manajemen. Detail kriteria & bobot didiskusikan terpisah sebelum implementasi.

## Kontrak Output (Mengikat)

Modul ini menghasilkan `ModuleOutput` — bentuknya dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6, **identik** untuk ketiga modul.

Bentuk yang sama untuk ketiganya bukan kompromi terhadap Prinsip #3 — justru sebaliknya. Wadah yang seragam membuat ketiga pandangan bisa ditampilkan berdampingan dan dibandingkan **tanpa harus disamakan isinya**. Yang wajib berbeda antar modul adalah kriteria, bobot, dan kesimpulannya; bukan struktur laporannya.

Aturan validasi yang berlaku ke modul ini (output **ditolak** kalau gagal):

| # | Aturan |
|---|---|
| V1 | Ada tepat satu entri `flag_responses` untuk setiap flag severity tinggi di Knowledge |
| V2 | `rationale` tiap flag_response spesifik per flag — rationale identik di ≥2 flag ditandai `generic_response` |
| V3 | `confidence.band != high` → `limiters` tidak boleh kosong |
| V4 | Field Knowledge berstatus `missing` yang relevan dengan kriteria modul ini wajib muncul di `knowledge_gaps` |
| V6 | `confidence.score` ≤ `ConfidenceReport.overall.score` — tak boleh lebih yakin dari data yang menopangnya |
| V5 | Keadaan `*_tak_terbaca` wajib menyebut `knowledge_gaps` penyebabnya |

`context_used` wajib menyebut komponen Layer 1 mana yang benar-benar mempengaruhi `stance` — terutama Yield Curve & Liquidity Conditions. Kalau `kind=derived` (mis. business_cycle_stage, market_sentiment), modul wajib memperlakukannya sebagai pembacaan yang dikonstruksi, bukan angka otoritatif (Prinsip #5).

### Kosakata `stance` — Milik Modul Ini Sendiri (D-09)

**Pertanyaan modul ini:** *Ini mesin compounding?*

**Kosakata:** `compounding_kuat` · `compounding_rapuh` · `bukan_compounder` · `mesin_tak_terbaca`

### Akses Field (D-12)

**Boleh baca:** 1, 2 (penuh), 3a, 4 (penuh), 5, 6, 7, peer
**Tidak boleh baca:** 3b (momentum), `CatalystSet`

### Kriteria & Tesis (D-13)

**Quality bertaruh pada kelanjutan, bukan perubahan (D-13).** Mesin ini jalan, dan pertanyaannya cuma apakah ia akan terus jalan — bukan apakah ada sesuatu yang baru akan terjadi.

**Kenapa tidak boleh membaca 3b (momentum) atau `CatalystSet`:** mesin yang awet tidak perlu sedang berakselerasi untuk dianggap awet. Kalau Quality boleh melihat momentum, ia perlahan tergoda mengandalkannya dan menempel ke Multibagger — dua lensa jadi satu lensa berbaju dua (D-12).

### Koreksi (16 Juli, gut-check pemilik produk): Reinvestasi ≠ Erosi

Versi awal: *"margin/FCF mulai turun → `compounding_rapuh`"*, tanpa peduli **kenapa** turunnya. Itu salah, dan kasus MSFT yang membuktikannya — margin turun karena CapEx $190B buat kapasitas AI, bukan karena kalah bersaing. Revenue tetap +18%, Azure re-akselerasi ke 40%, FCF masih deras cuma lagi ketutup investasi. Itu mesin yang lagi nambah bahan bakar, bukan mesin yang bocor. Kriteria lama tidak membedakan itu dari INTC, yang rugi $2.4B di segmen yang **memang belum profit** plus writedown $4.1B mengakui akuisisi lama gagal — situasi yang secara kategori berbeda.

**Tes pembeda, sebelum menilai arah margin:** apakah penurunan margin **didanai kekuatan** (revenue tetap kuat/berakselerasi, level margin & FCF absolut masih jauh di atas ambang gagal, CapEx naik dengan alasan terungkap di filing) — atau **tanda erosi** (revenue ikut melambat/turun, tidak ada penjelasan reinvestasi, atau kerugian di segmen yang seharusnya sudah profit)?

| Stance | Syarat |
|---|---|
| `compounding_kuat` | Margin stabil/naik, **ATAU** margin turun tapi revenue tetap kuat/berakselerasi **dan** level margin & FCF absolut masih tinggi **dan** penurunan dijelaskan CapEx/reinvestasi terungkap (bukan tekanan biaya/kompetisi) · FCF positif berturut · konsentrasi revenue di bawah ambang · percentile margin peer di atas median · tanpa flag governance severity tinggi. Kalau masuk lewat jalur reinvestasi, tempelkan `limiter: "margin tertekan siklus reinvestasi — pantau apakah revenue/margin pulih dalam N kuartal"` |
| `compounding_rapuh` | Margin/FCF turun **dan** revenue ikut melambat/flat, **atau** penurunan tidak dijelaskan CapEx/reinvestasi — tanda erosi, bukan investasi terencana. 3a (model bisnis, posisi kompetitif) masih utuh |
| `bukan_compounder` | Kerugian struktural di segmen yang seharusnya sudah profit, atau restatement, atau margin & FCF negatif bersamaan tanpa penjelasan reinvestasi — mesin sudah jadi mesin lain |
| `mesin_tak_terbaca` | Field kunci di 2/3a/7 `missing` melewati ambang, termasuk kalau alasan CapEx tidak terungkap di filing (tidak bisa menjalankan tes pembeda) |

**Draft komitmen v2, diuji ke 3 kasus, direvisi 16 Juli setelah gut-check:** PG → `compounding_kuat` (FCF productivity 82%, dividen naik 70 tahun berturut). INTC → `bukan_compounder` (foundry operating loss $2.4B/kuartal di segmen yang belum profit, restructuring $4.1B mengakui akuisisi gagal — bukan reinvestasi, tapi menutup lubang). **MSFT → `compounding_kuat` (revisi dari `compounding_rapuh`)** — GM 67.6% memang tersempit sejak 2022, tapi revenue tetap +18% dan CapEx $190B dijelaskan eksplisit sebagai kapasitas AI di filing. Lolos tes pembeda: didanai kekuatan, bukan tanda erosi. Ditempeli `limiter: "margin tertekan siklus reinvestasi AI — pantau pemulihan margin 3-4 kuartal ke depan"`.

Ini tetap **tanpa pernah melihat Azure +40%** (itu 3b, tetap tidak diakses) — koreksinya bukan membuka akses field baru, tapi memperbaiki *bagaimana* field yang sudah diakses (bagian 2: revenue growth, CapEx beserta alasannya) dipakai menilai arah margin.

Kosakata ini **tidak dipakai modul lain dan tidak bisa dipetakan ke kosakata modul lain** — pemetaan semacam itu dilarang dibuat (D-09). Versi sebelumnya memakai satu enum bersama (`compelling/interesting/weak`) untuk ketiga modul; itu skala ordinal yang langsung berubah jadi hitungan suara di kepala pembaca.

Di dalam kosakata ini urutan tetap ada dan sah — satu lensa, satu sumbu. Itulah yang membuat daftar lensa ini bisa diurutkan (D-08) tanpa melanggar Prinsip #3: yang dilarang adalah **menggabung** lensa, bukan mengurutkan di dalam satu lensa.

## Independensi

Modul ini **tidak boleh** memanggil, membaca state, atau bergantung pada urutan eksekusi terhadap dua modul lain (Prinsip #3). Dites lewat T1 & T2 di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

Kalau nanti ketiga modul berbagi komponen (prompt/template reasoning yang sama), komponen bersama itu wajib dicek tidak diam-diam menyamakan kesimpulan — kalau iya, Multi-Lens tinggal klaim di atas kertas.

## Terhubung Dengan

Menerima Market Context dari Layer 1, Knowledge & Peer Comparison dari Layer 2. Hasilnya masuk ke Aggregator.

---

© AlphaForge v2
