# Module — Quality / Compound

**Status:** Aktif — revisi: kontrak output & aturan validasi flag ditambahkan (D-04)
**Doc version:** 2.0.0

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
| V5 | `stance = not_applicable` wajib disertai `stance_rationale` |

`context_used` wajib menyebut komponen Layer 1 mana yang benar-benar mempengaruhi `stance` — terutama Yield Curve & Liquidity Conditions. Kalau `kind=derived` (mis. business_cycle_stage, market_sentiment), modul wajib memperlakukannya sebagai pembacaan yang dikonstruksi, bukan angka otoritatif (Prinsip #5).

## Independensi

Modul ini **tidak boleh** memanggil, membaca state, atau bergantung pada urutan eksekusi terhadap dua modul lain (Prinsip #3). Dites lewat T1 & T2 di `01_ARCHITECTURE/04_DATA_CONTRACTS.md` §6.

Kalau nanti ketiga modul berbagi komponen (prompt/template reasoning yang sama), komponen bersama itu wajib dicek tidak diam-diam menyamakan kesimpulan — kalau iya, Multi-Lens tinggal klaim di atas kertas.

## Terhubung Dengan

Menerima Market Context dari Layer 1, Knowledge & Peer Comparison dari Layer 2. Hasilnya masuk ke Aggregator.

---

© AlphaForge v2
