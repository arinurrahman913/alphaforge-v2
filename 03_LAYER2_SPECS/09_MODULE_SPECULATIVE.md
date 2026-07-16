# Module — Speculative

**Status:** Aktif — revisi: kontrak output & aturan validasi flag ditambahkan (D-04)
**Doc version:** 3.0.0

## Definisi

Salah satu dari tiga modul reasoning independen. Fokus pada peluang momentum/katalis dengan profil risk-reward asimetris, termasuk saham yang secara fundamental belum solid tapi punya katalis besar.

## Kenapa Penting

Peluang spekulatif punya karakter risiko dan horizon waktu yang sangat berbeda dari Multibagger maupun Quality/Compound — modul terpisah mencegah sinyal ini tenggelam atau justru mendominasi penilaian saham yang sebetulnya lebih cocok dinilai dari lensa lain.

## Sumber Data / Pendekatan

Knowledge + Market Context Package, terutama VIX dan Market Sentiment sebagai indikator risk appetite. Dilengkapi Catalyst Tracking.

## Cara Kerja

Reasoning modul ini menilai: ada tidaknya katalis konkret dan waktunya, besaran potensi upside vs downside, kondisi risk appetite market saat ini. Detail kriteria & bobot didiskusikan terpisah sebelum implementasi.

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

`context_used` wajib menyebut komponen Layer 1 mana yang benar-benar mempengaruhi `stance` — terutama VIX & Market Sentiment. Kalau `kind=derived` (mis. business_cycle_stage, market_sentiment), modul wajib memperlakukannya sebagai pembacaan yang dikonstruksi, bukan angka otoritatif (Prinsip #5).

### Kosakata `stance` — Milik Modul Ini Sendiri (D-09)

**Pertanyaan modul ini:** *Ada asimetri berkatalis?*

**Kosakata:** `asimetri_berkatalis` · `asimetri_tanpa_katalis` · `tanpa_asimetri` · `asimetri_tak_terbaca`

### Akses Field (D-12)

**Boleh baca:** 1, volatilitas (4), 5, `CatalystSet` (penuh), konteks makro
**Tidak boleh baca:** 2 (kesehatan finansial), 3 (posisi kompetitif), 6 (valuasi)

### Kriteria & Tesis (D-13)

**Speculative bertaruh pada resolusi peristiwa, bukan pada kelanjutan atau trajectory (D-13).** Butuh tanggal — itu yang membedakannya dari Multibagger, yang trajectory-nya jalan terus dengan atau tanpa satu peristiwa tertentu.

**Kenapa tidak boleh membaca valuasi (6):** ini batas lensa, bukan gaya bicara. Asimetri berkatalis tidak butuh harga masuk yang wajar untuk jadi asimetri — kalau boleh melihat valuasi, Speculative akan pelan-pelan berubah jadi Quality yang gugup.

| Stance | Syarat |
|---|---|
| `asimetri_berkatalis` | ≥1 katalis `scheduled`/`expected` dalam horizon · hasil genuinely tidak pasti (ada gap guidance-vs-konsensus, atau outcome biner regulasi/trial) · volatilitas cukup untuk asimetri berarti |
| `asimetri_tanpa_katalis` | Tidak ada katalis dalam horizon, tapi volatilitas tetap tinggi |
| `tanpa_asimetri` | Tidak ada katalis, volatilitas rendah |
| `asimetri_tak_terbaca` | `CatalystSet.status` degraded/missing |

**Draft komitmen pertama, diuji ke 3 kasus (D-13):** PG → `tanpa_asimetri`. INTC → `asimetri_berkatalis` (ramp 18A, guidance $1.3B di atas konsensus). MSFT → `asimetri_berkatalis` (restrukturisasi kemitraan OpenAI 27 April, capex $3.4B di bawah konsensus meski beat semua metrik lain — celah ekspektasi yang jelas).

Kosakata ini **tidak dipakai modul lain dan tidak bisa dipetakan ke kosakata modul lain** — pemetaan semacam itu dilarang dibuat (D-09). Versi sebelumnya memakai satu enum bersama (`compelling/interesting/weak`) untuk ketiga modul; itu skala ordinal yang langsung berubah jadi hitungan suara di kepala pembaca.

Di dalam kosakata ini urutan tetap ada dan sah — satu lensa, satu sumbu. Itulah yang membuat daftar lensa ini bisa diurutkan (D-08) tanpa melanggar Prinsip #3: yang dilarang adalah **menggabung** lensa, bukan mengurutkan di dalam satu lensa.

## Independensi

Modul ini **tidak boleh** memanggil, membaca state, atau bergantung pada urutan eksekusi terhadap dua modul lain (Prinsip #3). Dites lewat T1 & T2 di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

Kalau nanti ketiga modul berbagi komponen (prompt/template reasoning yang sama), komponen bersama itu wajib dicek tidak diam-diam menyamakan kesimpulan — kalau iya, Multi-Lens tinggal klaim di atas kertas.

## Terhubung Dengan

Menerima Market Context dari Layer 1, Knowledge dari Layer 2, dan bekerja sama erat dengan Catalyst Tracking. Hasilnya masuk ke Aggregator.

---

© AlphaForge v2
