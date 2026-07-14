# Layer 2 — Stock Analysis Engine

**Status:** Aktif

---

## 1. Tujuan

Layer 2 menjawab pertanyaan: **"Di dalam kondisi market saat ini, saham mana yang layak diperhatikan, dan dari sudut pandang apa?"**

Berbeda dari v1 yang langsung memilih 1 skor komposit, Layer 2 sengaja mempertahankan tiga sudut pandang berbeda sampai ke output akhir.

## 2. Tahapan

### 2.1 Screening
Menyaring seluruh market (NASDAQ + NYSE) menjadi kandidat yang layak diproses lebih dalam. Filter awal harus murah secara komputasi (market cap minimum, volume minimum, kelengkapan data) — detail di `03_LAYER2_SPECS/01_SCREENING.md`.

### 2.2 Evidence
Mengumpulkan fakta terverifikasi per ticker: data keuangan, harga, kepemilikan institusional, berita. Tahap ini murni pengumpulan fakta, belum ada interpretasi.

### 2.3 Knowledge
Menyusun fakta dari Evidence menjadi pemahaman terstruktur — semacam profil perusahaan yang siap dipakai sebagai bahan reasoning. Knowledge tidak pernah dibangun sebelum Evidence ada (lihat Prinsip #1 di `00_Foundation/02_PRINCIPLES.md`).

### 2.4 Confidence & Peer Comparison
Dua proses yang menempel di tahap Knowledge:
- **Confidence/Data Quality** — menandai seberapa kuat evidence yang mendasari knowledge ini.
- **Peer/Relative Comparison** — membandingkan saham dengan kompetitor/industri sejenis, supaya jelas apakah suatu angka "bagus" secara absolut atau relatif.

### 2.5 Risk / Red-Flag Check
Gerbang pemeriksaan sebelum Knowledge diteruskan ke modul reasoning manapun. Kalau ditemukan red flag serius (masalah akuntansi, governance, litigasi besar), ini ditandai secara eksplisit dan berlaku untuk ketiga modul di bawah — bukan hanya jadi bagian dari salah satu modul.

### 2.6 Tiga Modul Reasoning
Masing-masing modul menerima Knowledge + Market Context yang sama, tapi bereasoning secara independen sesuai lensanya:

- **Multibagger Module** — potensi pertumbuhan eksplosif jangka panjang.
- **Quality/Compound Module** — kualitas dan konsistensi compounder.
- **Speculative Module** — momentum/katalis dengan risk-reward asimetris, dilengkapi Catalyst Tracking.

### 2.7 Aggregator & Output
Menyusun ketiga hasil untuk ditampilkan berdampingan. Tidak ada penggabungan jadi satu angka — investor melihat ketiganya dan memilih lensa yang relevan dengan tujuannya.

### 2.8 Historical Tracking / Decision Journal
Setelah output dikeluarkan, hasilnya disimpan sebagai catatan historis, untuk divalidasi terhadap kenyataan di masa depan.

## 3. Diagram Ringkas

```
Screening → Evidence → Knowledge → [Confidence + Peer Comparison]
                                            ↓
                                Risk / Red-Flag Check
                                            ↓
                ┌───────────────┬──────────────────┬─────────────────┐
                ▼               ▼                  ▼
          Multibagger      Quality/Compound     Speculative
                                                 (+ Catalyst Tracking)
                └───────────────┴──────────────────┴─────────────────┘
                                        ↓
                                  Aggregator
                                        ↓
                                    Output
                                        ↓
                        Historical Tracking / Decision Journal
```

## 4. Kenapa Screening Market-Wide, Bukan Watchlist

v1 terbatas pada ~33 ticker tema tertentu (AI, Space, Energy) yang dipilih manual. Ini berarti peluang bagus di luar tema itu tidak pernah terlihat. v2 menyaring seluruh NASDAQ + NYSE, sehingga cakupannya tidak dibatasi bias pemilihan manual di awal.

---

© AlphaForge v2
