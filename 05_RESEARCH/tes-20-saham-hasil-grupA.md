# Tes 20 Saham — Hasil Grup A

**Lanjutan dari:** `tes-20-saham-draft.md`
**Tanggal:** 16 Juli 2026
**Data:** rilis publik Q3 FY2026 (April 2026). Verifikasi ulang sebelum dipakai. Bukan analisa investasi.

---

## 0. Ringkasan

Dugaan saya di draft: *"bahkan dengan akses field dibatasi, grup A tetap akan sepakat."*

**Setengah benar.** PG persis seperti dugaan. **MSFT membantah saya telak** — dan bantahan itu memunculkan cacat yang jauh lebih dalam dari yang sedang saya tes.

Cacatnya ada di D-08. Buatan saya, kemarin.

---

## 1. PG — Dugaan Benar

Data: Q3 FY2026 (24 April 2026). Net sales $21.2B (+7%), organic +3% (volume +2%, harga +1%), core EPS $1.59 (+3%).

| Lensa | Fakta yang dipegang (sesuai izin akses) | Stance |
|---|---|---|
| **Multibagger** | Organic +3%. Sepuluh kategori tumbuh, tapi semuanya tipis. Mega cap consumer staples | `ruang_tertutup` |
| **Quality** | Core GM 50.0%, core op margin 22.2% (dua-duanya ~1pt di bawah LY). Adjusted FCF productivity **82%**. Dividen naik **70 tahun berturut**. $10B dividen + $5B buyback | `compounding_kuat` |
| **Speculative** | Volatilitas rendah. Tidak ada katalis emiten. Manajemen menunda guidance FY27 karena perang Iran | `tanpa_asimetri` |

Tiga label berbeda. **Sepakat: kosong. Berbeda: 3.**

Secara mekanis, PG terlihat **lebih divergen dari INTC** — INTC juga tiga label berbeda, tapi PG menyapu bersih semua sumbu.

Itu absurd, dan di situ letak masalahnya.

---

## 2. MSFT — Dugaan Saya Salah

Saya taruh MSFT di grup A dengan alasan *"bagus di semua sudut — kalau ketiganya bilang bagus, apa gunanya tiga?"*

Ternyata datanya jauh dari itu. Q3 FY2026 (29 April 2026):

| Lensa | Fakta yang dipegang | Stance |
|---|---|---|
| **Multibagger** | Revenue $82.9B **+18%**, Azure **+40%** (di atas guidance sendiri 37–38%), Microsoft Cloud tembus $54.5B pertama kali. Tapi basisnya mega cap | `ruang_sempit` |
| **Quality** | **GM 67.6% — tersempit sejak 2022.** Capex kalender 2026 **$190B, naik 61%**, dan **$25B dari itu murni inflasi harga komponen, bukan tambahan kapasitas**. FCF menurun. Headcount turun di 2027 | `compounding_rapuh` |
| **Speculative** | Kemitraan OpenAI **direstrukturisasi 27 April** — rev-share keluar dihapus, lisensi non-eksklusif sampai 2032. Capex Q3 $31.9B, **$3.4B di bawah konsensus**. Saham **turun 3.9% padahal beat semua metrik** | `asimetri_berkatalis` |

**`compounding_rapuh` untuk Microsoft.** Itu panggilan yang keras, dan saya berdiri di belakangnya — di bawah lensa yang tidak boleh melihat katalis, yang tersisa adalah: margin tersempit dalam 4 tahun, FCF menurun, dan reinvestasi naik 61% yang seperdelapannya cuma bayar harga komponen yang lebih mahal. Mesin yang harus bayar makin mahal untuk output yang sama sedang melemah, sekuat apa pun permintaannya.

Lensa quality **tidak boleh tahu** Azure +40%. Itu bukan kelalaian — itu batas lensanya (`CatalystSet` ❌, dan pertumbuhan segmen masuk lewat posisi kompetitif yang... ✅ untuk quality). **Ini lubang di peta akses saya** — lihat §5.

**Kombinasi ini mengejutkan.** Kamu tidak bisa menebak `compounding_rapuh` dari `ruang_sempit`. Kamu tidak bisa menebak `asimetri_berkatalis` dari keduanya.

---

## 3. Temuan Utama: Divergensi ≠ Informasi

Bandingkan:

| | Multibagger | Quality | Speculative | Jumlah divergensi |
|---|---|---|---|:---:|
| **PG** | `ruang_tertutup` | `compounding_kuat` | `tanpa_asimetri` | **3** |
| **MSFT** | `ruang_sempit` | `compounding_rapuh` | `asimetri_berkatalis` | **3** |
| **INTC** | `ruang_terbuka` | `bukan_compounder` | `asimetri_berkatalis` | **3** |

**Ketiganya punya skor divergensi identik.** Menurut D-08, ketiganya duduk sejajar di daftar Divergensi.

Tapi lihat apa yang sebenarnya terjadi:

> **PG:** kasih tahu saya `compounding_kuat` untuk consumer staples $400B. Saya bisa tebak dua lensa lainnya **dengan hampir pasti**. Tidak ada satu pun yang saya belum tahu.
>
> **MSFT:** kasih tahu saya `ruang_sempit`. Coba tebak lensa quality-nya. **Kamu akan bilang `compounding_kuat`.** Salah.

Label yang berbeda **bukan** informasi. Yang informasi adalah **ketidakterdugaan**.

PG menghasilkan tiga label berbeda yang **saling menentukan sepenuhnya**. Multi-lens tidak membelikan apa pun di PG — satu lensa cukup, dua sisanya bisa diturunkan.

### Ini membatalkan metrik D-08

D-08 aturan **L5**: *"Daftar Divergensi mengurut menurut jumlah & kedalaman divergensi."*

**Jumlah divergensi adalah metrik yang salah.** PG akan naik ke puncak daftar Divergensi — tiga sumbu berbeda! — sambil tidak mengajarkan apa pun. Daftar yang seharusnya menunjukkan "di mana multi-lens membayar dirinya sendiri" justru akan dipenuhi saham paling membosankan di market, karena saham membosankan justru yang paling rapi menyapu ketiga sumbu.

Saya membangun daftar itu kemarin dan yakin ia jawabannya. Ia mengukur benda yang salah.

---

## 4. Perbaikannya — dan Ia Bisa Dihitung

Metrik yang benar: **seberapa tidak terduga kombinasi stance ini, dibanding populasi.**

Dan ini bagian yang bagus — **kamu sudah punya populasinya.** Satu sesi menghasilkan ~2.000 `AggregatorOutput`. Dari situ, sebaran bersama stance bisa dihitung langsung, dalam sesi, tanpa data historis:

```
P(quality_stance | multibagger_stance)   ← dihitung dari ~2.000 kandidat sesi ini

surprise(saham) = −log P(kombinasi stance saham ini | populasi sesi ini)
```

Contoh yang bakal keluar (ilustratif — angka asli butuh sesi penuh):

| Kombinasi | Frekuensi populasi | Surprise |
|---|---|---|
| `ruang_tertutup` + `compounding_kuat` + `tanpa_asimetri` (PG) | sangat lumrah | **rendah** |
| `ruang_sempit` + `compounding_rapuh` + `asimetri_berkatalis` (MSFT) | jarang | **tinggi** |
| `ruang_terbuka` + `bukan_compounder` + `asimetri_berkatalis` (INTC) | jarang | **tinggi** |

Daftar Divergensi diurutkan menurut **surprise**, bukan jumlah divergensi.

Efek sampingnya bagus: metrik ini **menemukan sendiri** kombinasi mana yang lumrah, tanpa kamu perlu menetapkannya. Dan ia otomatis menyesuaikan tiap sesi — kalau seluruh market bergerak, yang "mengejutkan" ikut bergeser.

### Bonus: T2 dapat definisi yang benar

D-04 tes **T2** bilang: *"kalau >90% ticker punya stance sama di ketiga modul, curigai Multi-Lens."*

Dengan kosakata terpisah (D-09), "stance sama" **tidak punya arti lagi** — ketiga kosakata tidak sebanding. T2 jadi tidak bisa dihitung sejak D-09 ditulis, dan saya tidak menyadarinya.

Definisi penggantinya jatuh dari metrik yang sama:

> **T2 (revisi):** kalau **mutual information** antar stance tiga lensa di seluruh populasi mendekati nol entropi bersyarat — artinya lensa B bisa ditebak dari lensa A — maka lensa B redundan. Multi-Lens tinggal klaim.

Ini bisa dites tiap sesi, otomatis, dengan satu angka. **Dan inilah tes tesis inti v2 yang saya bilang butuh berbulan-bulan.** Ternyata bisa dihitung dari satu sesi penuh.

---

## 5. Lubang di Peta Akses Saya — Ditemukan oleh MSFT

Lensa quality MSFT saya bilang `compounding_rapuh` tanpa melihat Azure +40%. Tapi cek peta akses draft: **bagian 3 (posisi kompetitif) = ✅ untuk quality.** Dan pertumbuhan segmen ada di bagian 3.

Jadi lensa quality **seharusnya** melihat Azure +40%. Saya melanggar peta saya sendiri.

Kalau dijalankan benar, quality melihat margin menyempit **dan** Azure +40%. Apakah stance-nya berubah jadi `compounding_kuat`? Mungkin. Dan kalau iya, PG dan MSFT jadi **sama** — dan temuan §3 saya runtuh.

**Ini pertanyaan terbuka paling penting yang tersisa,** dan saya tidak bisa menjawabnya sendiri:

> Bagian 3 (posisi kompetitif) memuat dua benda berbeda: **struktur** (model bisnis, konsentrasi revenue, moat) dan **momentum** (pertumbuhan segmen). Lensa quality butuh yang pertama. Yang kedua adalah bahan multibagger.
>
> **Bagian 3 harus dipecah?**

Kalau ya, peta akses jadi lebih halus dari yang saya draft, dan Knowledge butuh bagian 3a/3b. Kalau tidak, lensa quality akan selalu tergoda momentum, dan `compounding_rapuh` untuk MSFT tidak akan pernah keluar.

Saya condong ke "harus dipecah" — tapi saya baru saja salah menebak MSFT, jadi bobot pendapat saya di sini seharusnya kamu turunkan.

---

## 6. Yang Belum Dikerjakan

**KO, JNJ, COST, AAPL** — belum. Saya butuh data Q2 2026 mereka yang belum saya tarik, dan menebak dari ingatan Januari akan menghasilkan analisa yang terlihat sama meyakinkannya tapi salah. Itu persis yang dilarang Prinsip #5.

Empat saham itu tidak akan mengubah temuan §3 — PG dan MSFT sudah cukup untuk menunjukkan jumlah-divergensi mengukur benda yang salah. Mereka **akan** membantu menjawab §5.

---

## 7. Yang Berubah di Repo Kalau Kamu Setuju

| Keputusan | Perubahan |
|---|---|
| **D-08 L5** | Urut menurut **surprise**, bukan jumlah divergensi |
| **D-04 T2** | Definisi ulang: mutual information antar lensa, bukan "stance sama" (yang sudah tidak bisa dihitung sejak D-09) |
| **D-07** | `Synthesis` butuh field `surprise` + `population_baseline` |
| **Baru — D-12** | Pembatasan akses field per modul. **Ini yang menciptakan lensanya**; tanpa ini tidak ada tiga lensa |
| **Terbuka** | Bagian 3 Knowledge dipecah struktur vs momentum? (§5) |

---

## 8. Bantah Saya

1. **`compounding_rapuh` untuk MSFT.** Kalau menurutmu Azure +40% mengalahkan margin tersempit sejak 2022, kriteria quality kamu jauh berbeda dari saya — dan §5 jawabannya "jangan dipecah".
2. **Bagian 3 dipecah struktur vs momentum.** Ini yang paling menentukan.
3. **Surprise sebagai metrik.** Ia butuh populasi utuh sebelum daftar bisa dirender — memperberat barrier Fase B yang sudah berat.
4. **PG benar-benar tidak informatif?** Mungkin "tiga lensa sepakat ini membosankan" itu sendiri berguna — sebagai penyaring, bukan sebagai temuan.

---

© AlphaForge v2 — draft kerja, bukan spec
