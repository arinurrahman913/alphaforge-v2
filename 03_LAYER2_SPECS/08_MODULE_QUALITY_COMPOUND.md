# Module — Quality / Compound

**Status:** Aktif — revisi: kontrak output & aturan validasi flag ditambahkan (D-04)
**Doc version:** 3.0.0

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

| Stance | Syarat |
|---|---|
| `compounding_kuat` | Margin (gross/operating) stabil atau naik N periode terakhir · FCF positif berturut · konsentrasi revenue di bawah ambang · percentile margin peer di atas median · tanpa flag governance severity tinggi yang belum direspons |
| `compounding_rapuh` | Margin/FCF mulai turun, tapi 3a (model bisnis, posisi kompetitif) masih utuh — mesin melambat, belum berubah bentuk |
| `bukan_compounder` | Kerugian struktural di bisnis inti, atau restatement, atau margin & FCF negatif bersamaan — mesin sudah jadi mesin lain |
| `mesin_tak_terbaca` | Field kunci di 2/3a/7 `missing` melewati ambang |

**Draft komitmen pertama, diuji ke 3 kasus (D-13):** PG → `compounding_kuat` (FCF productivity 82%, dividen naik 70 tahun berturut). INTC → `bukan_compounder` (GAAP EPS −0.73, foundry operating loss $2.4B/kuartal, restructuring $4.1B). **MSFT → `compounding_rapuh`** — GM 67.6% tersempit sejak 2022, capex kalender 2026 naik 61% dengan $25B dari itu murni inflasi harga komponen (bukan kapasitas tambahan). Diambil **tanpa pernah melihat Azure +40%** (itu 3b) — pembuktian langsung bahwa D-12 menghasilkan stance yang berbeda dari kalau semua field dibuka.

Kosakata ini **tidak dipakai modul lain dan tidak bisa dipetakan ke kosakata modul lain** — pemetaan semacam itu dilarang dibuat (D-09). Versi sebelumnya memakai satu enum bersama (`compelling/interesting/weak`) untuk ketiga modul; itu skala ordinal yang langsung berubah jadi hitungan suara di kepala pembaca.

Di dalam kosakata ini urutan tetap ada dan sah — satu lensa, satu sumbu. Itulah yang membuat daftar lensa ini bisa diurutkan (D-08) tanpa melanggar Prinsip #3: yang dilarang adalah **menggabung** lensa, bukan mengurutkan di dalam satu lensa.

## Independensi

Modul ini **tidak boleh** memanggil, membaca state, atau bergantung pada urutan eksekusi terhadap dua modul lain (Prinsip #3). Dites lewat T1 & T2 di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

Kalau nanti ketiga modul berbagi komponen (prompt/template reasoning yang sama), komponen bersama itu wajib dicek tidak diam-diam menyamakan kesimpulan — kalau iya, Multi-Lens tinggal klaim di atas kertas.

## Terhubung Dengan

Menerima Market Context dari Layer 1, Knowledge & Peer Comparison dari Layer 2. Hasilnya masuk ke Aggregator.

---

© AlphaForge v2
