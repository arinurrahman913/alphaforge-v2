# Kontrak Data (Data Contracts)

**Status:** Aktif
**Doc version:** 1.2.0

---

## 1. Kenapa Dokumen Ini Ada

`01_SYSTEM_OVERVIEW.md` §2 menyatakan: *"Setiap tahap hanya menerima output dari tahap sebelumnya lewat paket yang jelas bentuknya."* Sampai revisi ini, "bentuknya" tidak pernah didefinisikan di manapun kecuali skema Knowledge. Market Context Package cuma punya contoh yang ditandai "ilustratif, bukan format final"; output tiga modul reasoning dan Aggregator tidak punya bentuk sama sekali.

Akibatnya dua klaim inti v2 menggantung di udara:
- Aggregator "menampilkan tiga hasil berdampingan" — berdampingan apanya, kalau bentuknya belum ada.
- Flag "wajib direspons" (Prinsip #4) dan confidence "menempel di tiap kesimpulan" (Prinsip #5) — dua kewajiban yang mengandaikan struktur yang belum ditulis.

Dokumen ini mengunci bentuknya. Isi reasoning-nya tetap urusan spec masing-masing modul; yang dikunci di sini cuma **wadahnya**.

Notasi di bawah bersifat deskriptif (bukan JSON Schema formal). Yang mengikat adalah nama field, tipe, dan aturan validasi — bukan format serialisasinya.

---

## 2. Aturan Umum

1. **Field kosong ditulis `null` + status `missing`.** Tidak pernah ditebak, diinterpolasi, atau diisi rata-rata industri. Berlaku di semua paket.
2. **Setiap paket membawa `method_version`.** Wajib untuk komponen derived/approximated (Prinsip #6). Format semver: perubahan formula yang mengubah hasil = naik minor/major.
3. **Setiap paket membawa `generated_at`** (ISO 8601, UTC).
4. **Paket tidak pernah dimutasi oleh tahap berikutnya.** Tahap hilir menghasilkan paket baru yang mereferensikan yang hulu, bukan menimpanya.

---

## 3. Market Context Package

Dihasilkan Layer 1. Dihitung **sekali per sesi** (`02_LAYER1_MARKET_CONTEXT.md` §4).

```
MarketContextPackage {
  session_id            : string
  generated_at          : timestamp
  components            : map<component_name, ComponentReading>
  context_summary       : ContextSummary            # D-06
}

ContextSummary {
  kind                  : "derived"                 # selalu — ini sintesis, bukan pembacaan
  method_version        : semver
  narrative             : string
  confidence            : {score, band, limiters}
  components_degraded   : [string]                  # komponen yang tidak utuh saat ini disusun
}

ComponentReading {
  name                  : string        # mis. "vix", "business_cycle_stage"
  value                 : any           # angka atau kategori, lihat spec komponen
  status                : enum{ok, degraded, missing}
  kind                  : enum{direct, derived}     # WAJIB — lihat catatan di bawah
  method_version        : semver | null             # wajib kalau kind=derived
  inputs                : [string]                  # nama komponen lain yang dipakai
  sources               : [{provider, fetched_at}]
  note                  : string | null             # alasan kalau degraded/missing
  narrative             : string | null             # arti pembacaan ini (D-06)
  narrative_version     : semver | null             # wajib kalau narrative terisi
}
```

`narrative` dihasilkan **pipeline**, bukan dashboard saat render (D-06). Narasi yang lahir saat render tidak berversi, tidak tersimpan, dan bisa berbeda tiap kali halaman dibuka untuk data yang sama — itu reasoning yang bersembunyi di lapisan tampilan.

### Catatan: kenapa `kind` wajib

Prinsip #5 mensyaratkan komponen derived/approximated menyatakan dirinya sebagai pembacaan yang dikonstruksi, bukan angka resmi. Sampai revisi ini, syarat itu cuma dipenuhi lewat kalimat prosa di dokumen spec — yang tidak ikut mengalir ke Layer 2. Field `kind` membuatnya ikut jalan bersama datanya, sehingga modul reasoning bisa memperlakukan `business_cycle_stage` (derived, disusun sendiri dari tiga indikator FRED) berbeda dari `vix` (direct, satu angka dari satu sumber).

`kind=direct`: yield_curve, vix, dxy, commodity_signals, market_regime, sector_rotation, liquidity_conditions, macro_calendar
`kind=derived`: business_cycle_stage, money_flow, market_breadth, market_sentiment

### Komponen yang gagal

Kalau satu komponen gagal dihitung, paket **tetap dikirim** dengan komponen itu `status=missing`. Layer 2 tidak boleh berhenti hanya karena satu dari dua belas komponen kosong — tapi modul reasoning yang menyandarkan kesimpulannya ke komponen itu wajib menurunkan confidence-nya (lihat §6).

---

## 4. Knowledge Profile

Skema lengkapnya di `03_LAYER2_SPECS/03_KNOWLEDGE.md`. Ringkasan bentuk paketnya:

```
KnowledgeProfile {
  ticker                : string
  evidence_snapshot_date: date
  method_version        : semver
  sections              : {
    identity            : {...}   # bagian 1
    financial_health    : {...}   # bagian 2
    competitive_position: {...}   # bagian 3
    historical_trends   : {...}   # bagian 4
    ownership           : {...}   # bagian 5
    valuation           : {...}   # bagian 6 — lihat D-02
    governance_events   : {...}   # bagian 7 — lihat D-01
  }
  screening_flags       : [string]              # diteruskan apa adanya dari Screening
  field_completeness    : {filled: int, expected: int, missing: [string]}
  sources               : [{provider, fetched_at}]
}
```

**Invariant yang mengikat (D-03):** `KnowledgeProfile` suatu ticker harus bisa dihitung dengan **hanya Evidence ticker itu** di tangan. Tidak ada field yang membutuhkan pengetahuan tentang ticker lain. Ini bisa dites langsung: jalankan Knowledge dengan Evidence satu ticker saja — kalau gagal atau menghasilkan field kosong yang seharusnya terisi, invariant-nya bocor.

---

## 5. Peer Comparison Result

Dihasilkan di Fase B, setelah barrier (lihat `03_LAYER2_STOCK_ANALYSIS.md` §2.4).

```
PeerComparisonResult {
  ticker                : string
  peer_group            : [string]              # ticker pembanding
  peer_group_basis      : string                # dasar pengelompokan (sektor/industri)
  peer_group_size       : int
  low_sample_size       : bool                  # true kalau size < 3
  peer_failures         : [{ticker, reason}]    # peer yang gagal ditarik — eksplisit, bukan silent skip
  metrics               : map<metric_name, {
                            value      : number | null,
                            peer_median: number | null,
                            percentile : number | null,
                            status     : enum{ok, missing}
                          }>
  method_version        : semver
}
```

`low_sample_size=true` tidak menggugurkan hasil — tapi Confidence wajib menurunkan skor bagian relatif, dan modul yang memakainya wajib menyebutnya.

---

## 6. Module Output — Kontrak Inti (D-04)

Berlaku identik untuk ketiga modul. **Bentuknya sama, isinya bebas** — itu justru yang membuat Multi-Lens bisa dibandingkan tanpa disamakan.

```
ModuleOutput {
  module                : enum{multibagger, quality_compound, speculative}
  ticker                : string
  method_version        : semver

  stance                : enum{compelling, interesting, weak, not_applicable}
  stance_rationale      : string              # kenapa stance itu, bahasa manusia

  confidence            : {
                            score   : number,          # 0–100
                            band    : enum{low, medium, high},
                            limiters: [string]         # WAJIB kalau band != high
                          }

  flag_responses        : [{
                            flag_id  : string,
                            impact   : enum{none, lowers_confidence, changes_stance, disqualifying},
                            rationale: string
                          }]

  context_used          : [{
                            component: string,         # nama komponen Layer 1
                            influence: string          # bagaimana ia mempengaruhi stance
                          }]

  knowledge_gaps        : [string]            # field missing yang membatasi kesimpulan ini
  generated_at          : timestamp
}
```

### Aturan Validasi — Output Ditolak Kalau Gagal

Ini bagian yang membuat Prinsip #4 dan #5 punya gigi. Bukan harapan — syarat.

| # | Aturan | Prinsip |
|---|---|---|
| V1 | Untuk **setiap** flag severity tinggi di Knowledge, harus ada tepat satu entri `flag_responses` dengan `flag_id` yang cocok. Jumlah tidak sama → **output ditolak**. | #4 |
| V2 | `flag_responses[].rationale` harus menyebut flag itu secara spesifik, bukan kalimat umum yang sama untuk semua flag. Rationale identik di ≥2 flag berbeda → **ditandai `generic_response`** untuk direview. | #4 |
| V3 | Kalau `confidence.band != high`, `limiters` tidak boleh kosong. | #5 |
| V4 | Kalau ada field Knowledge berstatus `missing` yang masuk kriteria modul ini, ia harus muncul di `knowledge_gaps`. | #5 |
| V5 | `stance = not_applicable` wajib punya `stance_rationale` — modul tidak boleh diam-diam mangkir dari saham yang sulit. | #3 |

> **Catatan V2:** log progres 14 Juli sudah mencatat gejalanya duluan — "respons red-flag di 3 modul masih level umum (moderate/elevated), belum spesifik per jenis flag". V1 memaksa modul menjawab; V2 memaksa jawabannya berbeda-beda. Tanpa V2, V1 gampang dipenuhi dengan menyalin kalimat yang sama tiga kali.

### Tes Independensi (Prinsip #3)

Prinsip #3 sudah mendefinisikan "independen" secara teknis, tapi belum pernah menyatakan cara mengeceknya. Dua tes yang bisa dijalankan langsung:

- **T1 — Urutan tidak berpengaruh.** Jalankan ketiga modul dengan input identik dalam urutan acak, berkali-kali. Semua `ModuleOutput` harus identik byte-per-byte tiap kali. Kalau tidak, ada state yang bocor antar modul.
- **T2 — Konvergensi mencurigakan.** Di satu batch analisa, kalau ketiga modul menghasilkan `stance` yang sama untuk >90% ticker, Multi-Lens-nya patut dicurigai jadi klaim di atas kertas. Ini bukan kegagalan otomatis — bisa saja market-nya memang seragam — tapi wajib memicu review, bukan lewat diam-diam.

---

## 7. Aggregator Output

```
AggregatorOutput {
  ticker                : string
  session_id            : string
  market_context_ref    : session_id                 # konteks apa yang berlaku saat itu
  knowledge_ref         : {ticker, evidence_snapshot_date}

  module_outputs        : [ModuleOutput]             # selalu 3, urutan lihat di bawah
  synthesis             : Synthesis                  # D-07
  risk_flags            : [Flag]                     # eksplisit, bukan disembunyikan
  confidence_overall    : {score, band, limiters}

  halted                : bool                       # true kalau berhenti di severity ekstrem
  halt_reason           : string | null

  method_versions       : map<component, semver>     # snapshot semua versi yang berlaku
  generated_at          : timestamp
}
```

### `Synthesis` — Peta Konvergensi (D-07)

```
Synthesis {
  method_version        : semver
  agreements            : [{claim, modules[], citations[]}]   # di mana ketiganya searah
  divergences           : [{
                            claim      : string,
                            modules    : [{module, position}],
                            root_cause : enum{different_fields, different_weights,
                                              knowledge_gap, flag_response, context_reading},
                            citations  : [string]
                          }]
  narrative             : string                    # rangkuman naratif, tunduk S2 & S3
  confidence            : {score, band, limiters}   # = TERENDAH dari 3 modul (S4)
  full_convergence      : bool                      # true kalau 3 stance sama → picu T2 (S5)
}
```

`root_cause` adalah inti gunanya. "Multibagger dan Quality/Compound berbeda" itu belum informasi — yang informasi adalah **kenapa**: apakah mereka membaca field yang berbeda, membobot field yang sama secara berbeda, salah satu terhalang `knowledge_gap`, atau berbeda dalam merespons flag yang sama.

`citations[]` wajib menunjuk ke field spesifik (`knowledge.valuation.pe_trailing`, `module.multibagger.knowledge_gaps[0]`). **Klaim yang tidak bisa disitasi tidak boleh ditulis** (S2). Ini yang mencegah `synthesis` menyelundupkan penilaian yang tidak dimiliki modul manapun.

`confidence` **disalin dari modul dengan confidence terendah**, tidak dihitung sendiri dan tidak dirata-rata (S4). Rangkuman tidak boleh lebih yakin dari input terlemahnya; rata-rata akan menyembunyikan satu modul yang buta di balik dua yang percaya diri.

`Synthesis` **dilarang** punya `verdict`, `score`, `rank`, `recommendation`, atau skor kesepakatan ("3/3 modul positif"). Yang terakhir itu terdengar seperti peta tapi sebenarnya skor — memampatkan tiga dimensi jadi satu, cuma menyamar sebagai statistik.

**Uji pembeda:** kalau `synthesis` bisa dibaca tanpa `module_outputs` dan tetap terasa cukup, ia sudah jadi verdict dan sudah gagal. `Synthesis` yang benar tidak berguna tanpa tiga kolom di atasnya — ia menunjuk ke sana.

### Urutan Tampilan

`module_outputs` **selalu** berurutan tetap: `multibagger`, `quality_compound`, `speculative`.

Urutan tetap dipilih secara sadar, bukan default. Alternatifnya — mengurutkan berdasarkan `confidence` atau `stance` — terdengar netral tapi justru menyelundupkan peringkat: modul teratas otomatis terbaca sebagai "jawabannya". Itu persis single-verdict lewat pintu belakang, yang seluruh Prinsip #3 dibuat untuk mencegahnya.

Urutan tetap tetap punya bias anchoring (yang pertama dibaca lebih dulu), tapi biasnya **konstan** — sama untuk semua saham, jadi tidak bisa disalahartikan sebagai sinyal tentang saham ini. Bias yang konstan bisa dikenali dan dikompensasi pembaca; bias yang berubah-ubah per saham tidak.

**Aggregator tidak boleh punya field `verdict`, `score`, `rank`, atau `recommendation`.** (`synthesis` bukan pengecualian — ia memetakan, tidak memampatkan; lihat D-07 dan uji pembeda di atas.) Bukan karena belum sempat — karena itu tugas yang sengaja tidak diberikan (`01_SYSTEM_OVERVIEW.md` §3).

### Kalau `halted = true`

Saham berhenti di Risk severity ekstrem (fraud terbukti, delisting). `module_outputs` kosong, `halt_reason` wajib terisi, `risk_flags` tetap lengkap. Entri tetap disimpan ke Historical Tracking — saham yang dihentikan tetap perlu bisa diaudit di kemudian hari.

---

## 8. Historical Entry

```
HistoricalEntry {
  entry_id              : string
  analyzed_at           : timestamp
  aggregator_output     : AggregatorOutput      # snapshot utuh, bukan ringkasan
  method_versions       : map<component, semver>
  outcome               : null                  # diisi belakangan saat evaluasi
}
```

`method_versions` disalin ke level entri (bukan cuma di dalam `aggregator_output`) supaya audit di masa depan bisa memfilter entri per versi metodologi tanpa membongkar isi snapshot — persis kebutuhan yang disebut Prinsip #6.

`outcome` sengaja `null` saat entri dibuat. Lihat `03_LAYER2_SPECS/12_HISTORICAL_TRACKING_JOURNAL.md` — **penyimpanan** berjalan sejak v2.0, **evaluasinya** yang boleh menyusul ke v2.1.

---

## 8b. Catatan: `context_summary` Bukan Skor Market

`ContextSummary` **dilarang** punya field skor tunggal (`market_score`, `risk_level` numerik, dsb). Ia mendeskripsikan kondisi, tidak merekomendasikan tindakan dan tidak memeringkat.

Larangan ini sejajar dengan larangan `verdict` di Aggregator, dan alasannya sama: begitu ada satu angka ringkasan, ia jadi satu-satunya hal yang dibaca orang dari dua belas komponen — dan sebelas kartu lainnya berubah jadi hiasan.

`components_degraded` wajib terisi supaya dashboard bisa menampilkan kekuatan pijakan kesimpulan ini. Ringkasan yang berdiri di atas 9 dari 12 komponen tidak boleh terbaca sama kuatnya dengan yang berdiri di atas 12.

## 9. Referensi

- Keputusan di balik kontrak ini → `00_Foundation/04_DECISIONS.md` (D-01 s/d D-05)
- Skema Knowledge lengkap → `03_LAYER2_SPECS/03_KNOWLEDGE.md`
- Bentuk Flag → `03_LAYER2_SPECS/04_RISK_REDFLAG_CHECK.md`
- Bagaimana paket ini ditampilkan → `01_ARCHITECTURE/05_DASHBOARD_LOCAL.md`

---

© AlphaForge v2
