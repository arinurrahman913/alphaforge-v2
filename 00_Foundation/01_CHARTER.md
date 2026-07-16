# AlphaForge v2 — Piagam (Charter)

**Status:** Aktif
**Doc version:** 1.1.0
**Menggantikan:** Tidak ada. v1 (`alphaforge-v1` / `alphaforge-core-v1`) tetap hidup sebagai referensi.

---

## 1. Kenapa v2 Dibuat

v1 berhasil membuktikan alur dasarnya jalan: ambil data 1 saham, kasih skor berdasarkan aturan, keluarkan laporan. Tapi dari penggunaan nyata, ada 3 kelemahan struktural yang gak bisa diperbaiki hanya dengan nambah fitur — harus dirombak dari intinya:

1. **Tidak ada konteks market.** Setiap saham dianalisa sendirian, seolah kondisi market, sektor, dan makro di sekitarnya tidak ada.
2. **Knowledge dibangun sebelum Evidence.** Spec engine v1 membangun "Knowledge" sebelum "Evidence" — terbalik dari cara pemahaman yang baik seharusnya terbentuk.
3. **Satu verdict untuk semua gaya.** v1 memaksa setiap saham masuk ke satu skor komposit, padahal "investasi yang bagus" artinya beda-beda tergantung apakah kamu mencari multibagger, compounder yang stabil, atau taruhan spekulatif dengan risk/reward asimetris.

v2 adalah pembangunan ulang inti reasoning dari nol untuk menutup ketiga celah ini.

---

## 2. Apa Itu v2

v2 terdiri dari 2 layer:

**Layer 1 — Market Context Engine**
Membaca kondisi makro dan market secara keseluruhan sebelum satu pun saham dianalisa. 12 komponen digabung jadi satu Market Context Package: Business Cycle Stage, Sector Rotation, Money Flow, Liquidity Conditions, Yield Curve, Market Breadth, Volatility (VIX), Market Regime, Macro Calendar, Currency (DXY), Commodity Signals, dan Market Sentiment.

**Layer 2 — Stock Analysis Engine**
Menyaring seluruh market (NASDAQ + NYSE, bukan watchlist manual), membangun Evidence terverifikasi per saham, menurunkan Knowledge dari evidence itu, melewati Risk/Red-Flag Check (flag serius menempel eksplisit dan wajib direspons ketiga modul — lihat Prinsip #4), lalu dianalisa lewat 3 lensa independen — Multibagger, Quality/Compound, dan Speculative — masing-masing dengan proses reasoning sendiri. Aggregator menampilkan ketiga pandangan berdampingan; tidak menggabungkannya jadi satu verdict.

Spec lengkap tiap komponen ada di `02_LAYER1_SPECS/` dan `03_LAYER2_SPECS/`. Bentuk paket data yang mengalir antar tahap dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md`; keputusan arsitektur beserta alternatif yang ditolak dicatat di `00_Foundation/04_DECISIONS.md`.

---

## 3. Apa yang Berubah dari v1

| | v1 | v2 |
|---|---|---|
| Kesadaran market | Tidak ada | Layer 1 khusus, dibaca sebelum analisa saham apa pun |
| Cakupan (universe) | Watchlist manual (~33 ticker) | Screening seluruh market (NASDAQ + NYSE) |
| Urutan data → pemahaman | Data langsung jadi skor | Evidence dulu, Knowledge diturunkan darinya |
| Penanganan risiko | Menyatu dalam scoring, tanpa pemeriksaan khusus | Risk/Red-Flag Check eksplisit, flag wajib direspons tiap modul reasoning |
| Lensa analisa | Satu skor komposit | Tiga lensa independen, ditampilkan bersamaan |
| Confidence | Tidak dilacak | Confidence/Data Quality menempel di tiap kesimpulan |
| Siklus belajar | Tidak ada | Historical Tracking / Decision Journal |
| Output akhir | Satu verdict | Tiga pandangan, tidak digabung — investor memilih lensa |

---

## 4. Apa yang Tetap Sama

- AlphaForge tidak meramal masa depan. Tujuannya meningkatkan kualitas *keputusan* investasi, bukan menjamin hasil.
- Evidence sebelum opini. Reasoning sebelum kesimpulan.
- Investor tetap bertanggung jawab atas keputusan akhir. AlphaForge memberi informasi; bukan memutuskan.

---

## 5. Hubungan dengan v1

v1 tidak dihapus, tidak di-deprecate, tidak digabung ke v2. Statusnya tetap hidup sebagai:

- Referensi kerja untuk pola yang masih relevan (struktur CLI, pola provider, layer reporting).
- Baseline untuk mengukur kualitas reasoning v2.
- Cadangan (fallback) kalau ada bagian pembangunan v2 yang macet.

Kedua repo (`alphaforge-v1` / `alphaforge-core-v1` dan pasangan v2-nya) didokumentasikan dan di-versioning secara independen. Progres di satu sisi tidak berarti apa-apa terhadap kondisi sisi lainnya.

---

## 6. Disiplin Ruang Lingkup

Layer 1 punya 12 komponen, Layer 2 punya 12 area spec — ini sudah cukup luas. Aturan tingkat charter ke depannya: tidak ada komponen top-level baru ditambahkan ke layer manapun tanpa dicek dulu apakah itu bisa diekspresikan sebagai varian dari sesuatu yang sudah ada di daftar. Kedalaman lebih diutamakan daripada pelebaran.

---

© AlphaForge v2
