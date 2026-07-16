# Module — Multibagger

**Status:** Aktif — revisi: kontrak output & aturan validasi flag ditambahkan (D-04)
**Doc version:** 2.1.0

## Definisi

Salah satu dari tiga modul reasoning independen. Fokus mengidentifikasi saham dengan potensi pertumbuhan eksplosif jangka panjang (multi-bagger).

## Kenapa Penting

Gaya investasi 'mencari saham 10x' butuh kriteria dan reasoning yang sangat berbeda dari mencari compounder stabil — modul terpisah memastikan lensa ini tidak tercampur/teredam oleh lensa lain.

## Sumber Data / Pendekatan

Knowledge + Market Context Package, terutama Sector Rotation dan Business Cycle Stage sebagai konteks pertumbuhan sektor.

## Cara Kerja

Reasoning modul ini menilai: skala pasar (TAM), tingkat pertumbuhan pendapatan, skalabilitas model bisnis, potensi ekspansi. Detail kriteria & bobot didiskusikan terpisah sebelum implementasi.

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

`context_used` wajib menyebut komponen Layer 1 mana yang benar-benar mempengaruhi `stance` — terutama Sector Rotation & Business Cycle Stage. Kalau `kind=derived` (mis. business_cycle_stage, market_sentiment), modul wajib memperlakukannya sebagai pembacaan yang dikonstruksi, bukan angka otoritatif (Prinsip #5).

### Kosakata `stance` — Milik Modul Ini Sendiri (D-09)

**Pertanyaan modul ini:** *Ada ruang untuk kelipatan besar?*

**Kosakata:** `ruang_terbuka` · `ruang_sempit` · `ruang_tertutup` · `ruang_tak_terbaca`

Kosakata ini **tidak dipakai modul lain dan tidak bisa dipetakan ke kosakata modul lain** — pemetaan semacam itu dilarang dibuat (D-09). Versi sebelumnya memakai satu enum bersama (`compelling/interesting/weak`) untuk ketiga modul; itu skala ordinal yang langsung berubah jadi hitungan suara di kepala pembaca.

Di dalam kosakata ini urutan tetap ada dan sah — satu lensa, satu sumbu. Itulah yang membuat daftar lensa ini bisa diurutkan (D-08) tanpa melanggar Prinsip #3: yang dilarang adalah **menggabung** lensa, bukan mengurutkan di dalam satu lensa.

## Independensi

Modul ini **tidak boleh** memanggil, membaca state, atau bergantung pada urutan eksekusi terhadap dua modul lain (Prinsip #3). Dites lewat T1 & T2 di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

Kalau nanti ketiga modul berbagi komponen (prompt/template reasoning yang sama), komponen bersama itu wajib dicek tidak diam-diam menyamakan kesimpulan — kalau iya, Multi-Lens tinggal klaim di atas kertas.

## Terhubung Dengan

Menerima Market Context dari Layer 1, Knowledge dari Layer 2. Hasilnya masuk ke Aggregator.

---

© AlphaForge v2
