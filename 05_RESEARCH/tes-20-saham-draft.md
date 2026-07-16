# Tes 20 Saham — Draft untuk Dibantah

**Status:** Draft. Bukan hasil. Dibuat untuk dibongkar.
**Tanggal:** 16 Juli 2026
**Peringatan data:** angka di bawah dari rilis publik Q1 FY2026 (April 2026) dan pengetahuan sampai Januari 2026. **Verifikasi ulang sebelum dipakai untuk apa pun.** Ini bukan analisa investasi — ini tes mekanisme yang kebetulan memakai perusahaan nyata sebagai bahan.

---

## 0. Koreksi: Ini Bukan Tes yang Saya Sarankan

Tes yang saya sarankan berbunyi: *"pilih 20 saham **yang kamu udah punya pandangan soal itu**."*

Bagian itu bukan hiasan — itu **alat ukurnya**. Tes aslinya membandingkan keluaran tiga lensa terhadap **prior kamu**. Pertanyaannya: *"apakah lensa-lensa ini ngasih saya sesuatu yang saya belum tahu?"* Tanpa prior kamu, tidak ada pembanding. Tidak ada "belum tahu".

Saya tidak punya pandangan kamu. Jadi versi ini **tidak bisa** menjawab pertanyaan itu.

Yang bisa dijawab versi ini lebih sempit, tapi tetap nyata dan tetap bisa gagal:

> **Kalau tiga lensa disusun dengan sungguh-sungguh berbeda, apakah mereka berselisih di saham nyata — atau runtuh jadi satu jawaban dalam tiga dialek?**

Itu keluhan #2 saya kemarin. Bisa dites tanpa kamu. **Jangan pakai ini sebagai pengganti tes kamu** — pakai sebagai sesuatu untuk dibantah, karena membantah proposal konkret biasanya lebih cepat memunculkan keyakinan daripada halaman kosong.

---

## 1. Dua Puluh Saham — dan Logika di Baliknya

Karena sumbu "yang kamu suka/nggak suka" hilang, saya ganti sumbunya jadi: **dirancang supaya multi-lens bisa gagal.**

Kalau saya cuma pilih saham yang jelas-jelas beda di tiap lensa, saya cuma membuktikan diri sendiri. Jadi hampir sepertiga daftar ini dipilih **karena saya menduga ketiga lensa akan sepakat** — kalau dugaan itu benar, itu bukti melawan multi-lens, dan itu justru yang perlu ditemukan.

### A. Risiko konvergensi — dugaan: ketiganya sepakat (6)
Kalau lensa runtuh, di sini ketahuannya.

| Ticker | Kenapa di sini |
|---|---|
| **KO** | Compounder mapan, tumbuh lambat, tanpa katalis. Ketiganya "harusnya" bilang hal yang sama dengan kata berbeda |
| **JNJ** | Sama, plus litigasi yang semua lensa lihat |
| **PG** | Uji paling murni: tidak ada yang menarik dari sudut mana pun |
| **COST** | Kualitas jelas, valuasi jelas mahal — dua fakta, semua lensa lihat keduanya |
| **MSFT** | Bagus di semua sudut. Kalau ketiganya bilang bagus, apa gunanya tiga? |
| **AAPL** | Kasus di mockup. Cek apakah "divergensi"-nya asli |

### B. Kandidat divergensi struktural — dugaan: ketiganya berselisih (6)

| Ticker | Kenapa di sini |
|---|---|
| **INTC** | **Kasus utama.** GAAP rugi tapi DCAI +22%; foundry bakar $2.4B/kuartal tapi 18A ramp. Tiap lensa punya fakta favoritnya sendiri |
| **PLTR** | Pertumbuhan vs valuasi vs katalis — tiga cerita berbeda dari satu perusahaan |
| **MRNA** | Pipeline = katalis; keuangan = rusak. Spekulatif dan quality harusnya bertabrakan keras |
| **NKE** | Compounder yang lagi patah. Lensa quality punya sejarah, spekulatif punya turnaround |
| **PFE** | Cliff paten vs dividen vs akuisisi |
| **TSM** | Kualitas + siklus + geopolitik sebagai katalis biner |

### C. Uji data buruk — dugaan: keadaan `*_tak_terbaca` menyala (4)
Kalau ketiga lensa tumbang bareng saat data kurang, `tak_terbaca` tidak berguna sebagai pembeda.

| Ticker | Kenapa di sini |
|---|---|
| **IONQ** | Nyaris tanpa fundamental bermakna. P/E null, EV/EBITDA null |
| **LUNR** | Small cap, filing tipis, peer group nyaris tidak ada |
| **RIVN** | Bakar kas — margin bermakna? |
| **TDOC** | Growth patah, goodwill impairment berulang |

### D. Uji sensitivitas konteks Layer 1 (4)
Apakah `context_used` benar-benar mengubah sesuatu, atau cuma dilampirkan?

| Ticker | Kenapa di sini |
|---|---|
| **ASML** | Siklus semi — Business Cycle Stage harusnya menggigit |
| **NVDA** | Ekstrem di tiap dimensi; sentimen harusnya relevan |
| **SOFI** | Sensitif yield curve secara langsung |
| **GME** | Sentimen adalah tesisnya. Kalau lensa quality tidak bilang `bukan_compounder` di sini, kriterianya rusak |

---

## 2. Tiga Lensa — dan Inti Seluruh Tes

Ini bagian yang penting. Sisanya turunan.

Kemarin saya khawatir divergensinya cuma **beda dialek, bukan beda pandangan** — karena ketiga modul baca `KnowledgeProfile` yang sama. Kalau input identik, yang bisa beda cuma bobot, dan `root_cause: different_weights` akan jadi jawaban tiap saat.

Draft ini mengambil jalan sebaliknya: **tiap lensa cuma boleh baca sebagian Knowledge.** Itu satu-satunya cara divergensinya jadi struktural.

### Peta Akses Field

| Bagian Knowledge | Multibagger | Quality/Compound | Speculative |
|---|:---:|:---:|:---:|
| 1 — Identitas | ✅ | ✅ | ✅ |
| 2 — Kesehatan finansial | **arah saja** | ✅ **penuh** | ❌ |
| 3 — Posisi kompetitif | ✅ | ✅ | ❌ |
| 4 — Tren historis | ✅ | ✅ **penuh** | **volatilitas saja** |
| 5 — Kepemilikan | ❌ | ✅ | ✅ |
| 6 — Valuasi | ✅ | ✅ | ❌ |
| 7 — Governance | ❌ | ✅ | ❌ |
| `CatalystSet` | ✅ | ❌ | ✅ **penuh** |
| Peer | ✅ | ✅ | ❌ |

Tiap ❌ adalah **klaim yang bisa salah**, dan tiap satu wajib dibela:

- **Speculative tidak boleh lihat kesehatan finansial (2).** Klaimnya: asimetri berkatalis tidak peduli margin. GME tidak naik karena neraca. Kalau lensa spekulatif boleh lihat bagian 2, ia akan pelan-pelan berubah jadi lensa quality yang gugup.
- **Speculative tidak boleh lihat valuasi (6).** Sudah ada di mockup: *"valuasi tidak relevan tanpa katalis."* Itu bukan gaya bicara — itu batas lensanya.
- **Multibagger tidak boleh lihat governance (7).** Klaimnya: dilusi & auditor itu urusan Risk, dan Risk sudah memaksa respons lewat `flag_responses`. Multibagger tidak perlu jalur kedua ke fakta yang sama.
- **Quality/Compound tidak boleh lihat `CatalystSet`.** Ini yang paling saya yakini. Compounder yang butuh katalis bukan compounder. Kalau lensa quality boleh lihat katalis, ia akan mulai membenarkan tesis lemah dengan peristiwa mendatang — persis penyakit yang mau kamu hindari.
- **Multibagger cuma lihat "arah" bagian 2**, bukan angkanya. Klaimnya: multibagger butuh tahu keuangannya membaik atau memburuk, tidak butuh ROE sampai dua desimal.

**Kalau kamu tidak setuju satu pun di atas, di situlah tesis kamu berada.** Itu tujuan dokumen ini.

### Tiga pertanyaan

| Lensa | Pertanyaan | Kosakata |
|---|---|---|
| Multibagger | Ada ruang untuk kelipatan besar? | `ruang_terbuka` · `ruang_sempit` · `ruang_tertutup` · `ruang_tak_terbaca` |
| Quality/Compound | Ini mesin compounding? | `compounding_kuat` · `compounding_rapuh` · `bukan_compounder` · `mesin_tak_terbaca` |
| Speculative | Ada asimetri berkatalis? | `asimetri_berkatalis` · `asimetri_tanpa_katalis` · `tanpa_asimetri` · `asimetri_tak_terbaca` |

---

## 3. INTC Dikerjakan Penuh — Kasus Uji Utama

Data: rilis Q1 FY2026 Intel (23 April 2026). Tiap lensa **cuma boleh menyentuh field yang diizinkan** di atas.

### Lensa 1 — Multibagger

Boleh lihat: identitas, arah bagian 2, posisi kompetitif, tren, valuasi, katalis, peer.

- Arah bagian 2: **membaik.** Revenue $13.6B (+7% YoY), non-GAAP GM 41% naik dari 39.2%, non-GAAP op margin 12.3% dari 5.4%.
- Posisi kompetitif: DCAI $5.1B **+22% YoY**. Bisnis terkait AI tumbuh 40% YoY, kini **60% dari revenue**.
- Yang belum berbayar: external foundry revenue cuma **$174 juta** — dari total foundry $5.4B. Opsi yang belum jadi apa-apa.

**Stance: `ruang_terbuka`** — bukan karena Intel bagus, tapi karena external foundry ~$0 adalah opsi yang belum dihargai. Kalau 18A menang pelanggan luar, sumbu revenue baru terbuka dari basis nol. Itu bentuk ruang.

**Confidence: rendah.** Tesisnya bersandar pada satu angka yang belum terjadi.

### Lensa 2 — Quality/Compound

Boleh lihat: bagian 2 **penuh**, posisi kompetitif, tren penuh, kepemilikan, valuasi, governance, peer. **Tidak boleh lihat katalis.**

- GAAP EPS **$(0.73)**. Bukan non-GAAP.
- Foundry operating loss **$2.4B satu kuartal**. Membaik $72 juta QoQ — sekitar **3%**.
- Restructuring & charges **$4.1B**, terutama goodwill impairment Mobileye. Akuisisi yang ditulis turun.
- Fab 34: beli balik 49% seharga **$7.7B tunai + $6.5B utang baru**.
- Adjusted FCF **−$2B**. Operating cash flow $1.1B, CapEx $5B.

**Stance: `bukan_compounder`** — bukan `compounding_rapuh`. Mesin yang bakar $2.4B per kuartal di satu segmen, menulis turun akuisisi $4.1B, dan menambah $6.5B utang untuk membeli aset yang sudah dipakainya, bukan mesin compounding yang sedang lemah. Itu mesin yang berbeda.

**Confidence: tinggi.** Fakta-faktanya tidak ambigu dan tidak butuh masa depan.

> Perhatikan lensa ini **tidak pernah menyentuh** DCAI +22% atau 18A ramp. Bukan karena mengabaikan — karena **tidak diizinkan melihatnya**. Katalis bukan bahan tesis compounding.

### Lensa 3 — Speculative

Boleh lihat: identitas, volatilitas, kepemilikan, `CatalystSet`. **Tidak boleh lihat bagian 2 atau valuasi.**

- Katalis: **ramp 18A** — `scheduled`, berlanjut sepanjang 2026. Yield "ahead of internal projections".
- Katalis: **guidance Q2 $13.8–14.8B vs konsensus ~$13.0B**. Selisih ~$1.3B. Ekspektasi tertinggal kenyataan.
- Katalis negatif: manajemen memandu **PC TAM turun low-double-digits di H2**. Terjadwal, diketahui, arah berlawanan.
- Volatilitas: tinggi.

**Stance: `asimetri_berkatalis`** — ada peristiwa terjadwal dengan hasil biner (yield 18A, menang/tidaknya pelanggan foundry luar), dan celah ekspektasi yang terukur.

**Confidence: sedang.** Dua katalis berlawanan arah di jendela yang sama.

### Peta konvergensi

| | |
|---|---|
| **Sepakat** | *(kosong)* — tidak ada satu pun klaim yang ketiganya sentuh |
| **Berbeda** | ruang_terbuka / bukan_compounder / asimetri_berkatalis |
| **`root_cause`** | **`different_fields`** — bukan `different_weights` |

**Ini temuannya.** Ketiga lensa tidak berselisih soal arti fakta yang sama. Mereka **memegang fakta yang berbeda sama sekali**:

- Multibagger memegang `external_foundry_revenue: $174M`
- Quality memegang `gaap_eps: -0.73`, `foundry_op_loss: -2.4B`
- Speculative memegang `catalyst: 18A_ramp`, `guidance_vs_consensus: +1.3B`

**Nol tumpang tindih.** Bagian "Sepakat" kosong bukan karena kebetulan — karena tidak ada field yang boleh dilihat lebih dari satu lensa di kasus ini.

---

## 4. Temuan

### Temuan utama: divergensi itu nyata — **tapi hanya kalau akses field dibatasi**

Kekhawatiran #2 saya kemarin benar, dan sekaligus bisa diobati.

Kalau ketiga lensa boleh baca ketujuh bagian Knowledge, ketiganya akan melihat GAAP rugi $(0.73) dan foundry loss $2.4B. Angka sekeras itu **menyeret semua lensa ke jawaban yang sama**. Kamu akan dapat tiga variasi dari "bagus tapi rusak", dan `root_cause` akan selalu `different_weights`.

Pembatasan akses **bukan optimasi**. Itu yang **menciptakan lensanya**. Tanpa itu kamu tidak punya tiga lensa — kamu punya satu lensa dengan tiga suara.

**Ini keputusan arsitektur, dan belum ada di spec manapun.** Kalau kamu setuju, ia perlu jadi D-12, dan `KnowledgeProfile` perlu punya kontrak akses per modul yang ditegakkan — bukan diharapkan.

### Temuan kedua: risiko konvergensi ada di grup A, bukan di INTC

INTC memang berselisih. Tapi INTC dipilih **karena** saya menduga begitu. Uji sesungguhnya ada di KO / PG / MSFT — dan di sana, dugaan saya: bahkan dengan akses field dibatasi, ketiganya masih akan sepakat. PG tidak punya katalis, tidak punya ruang, dan compounding-nya jelas. Tiga lensa akan bilang tiga hal yang semuanya berarti "ya sudah".

Kalau itu benar, **itu bukan kegagalan** — itu memberi tahu di mana multi-lens berguna: pada perusahaan yang sedang berubah, bukan pada perusahaan yang mapan. Dan itu punya konsekuensi keras ke Screening: mungkin funnel-nya seharusnya menyaring **untuk perubahan**, bukan untuk kualitas.

Saya tidak bisa memastikan tanpa mengerjakan enam saham grup A. Belum saya kerjakan — sengaja. Lihat §5.

### Temuan ketiga: `Sepakat` yang kosong itu mencurigakan

Di INTC, bagian `agreements[]` **kosong sama sekali**. Kalau itu terjadi di banyak saham, `synthesis` kehilangan separuh gunanya — peta yang cuma punya divergensi bukan peta, itu daftar.

Mungkin peta akses saya terlalu ekstrem. Mungkin harus ada **inti bersama** yang ketiganya lihat (bagian 1 + arah bagian 2?), supaya `agreements[]` punya bahan. Ini pertanyaan terbuka yang saya buat sendiri dan tidak bisa saya jawab tanpa kamu.

---

## 5. Yang Sengaja Tidak Saya Kerjakan

**Sembilan belas saham sisanya.**

Bukan karena kehabisan waktu. Kalau saya kerjakan 60 analisa, kamu akan punya dokumen rapi berisi **tesis investasi saya**, dan tekanan untuk menerimanya karena sudah terlanjur ada. Itu persis kegagalan #6 dengan lebih banyak halaman.

Satu saham cukup untuk membuktikan mekanismenya bisa berselisih. Sembilan belas lagi cuma menumpuk penilaian saya di atas penilaianmu.

**Yang saya kerjakan:** seleksi 20 dengan logika yang bisa kamu bantah, peta akses field yang bisa kamu tolak baris per baris, dan satu kasus penuh yang menunjukkan divergensi struktural itu mungkin.

**Yang harus kamu kerjakan:** enam saham grup A. Di situ tesnya menggigit — bukan di INTC.

---

## 6. Bantah Saya Di Sini

Urut dari yang paling mungkin salah:

1. **Quality tidak boleh lihat katalis.** Ini yang paling saya yakini dan karena itu paling perlu diserang. Kalau kamu pikir compounder yang punya katalis lebih baik dari yang tidak — peta akses saya salah, dan lensa quality kamu berbeda dari saya.
2. **Speculative buta terhadap valuasi.** Kalau menurutmu asimetri butuh harga masuk yang wajar, ❌ di bagian 6 harus jadi ✅ — dan seluruh divergensi INTC berubah.
3. **`bukan_compounder`, bukan `compounding_rapuh` untuk INTC.** Saya menarik garis di "mesin yang berbeda". Kalau menurutmu Intel compounder yang lagi sakit, kriteria quality kamu jauh lebih longgar dari saya — dan itu **informasi paling berharga** yang bisa keluar dari dokumen ini.
4. **`ruang_terbuka` untuk INTC.** Saya sandarkan pada external foundry $174M sebagai opsi. Itu bisa dibilang mengarang optionality dari kegagalan.
5. **Komposisi 20 saham.** Enam risiko-konvergensi terlalu banyak? Terlalu sedikit?

Balas dengan nomornya saja kalau mau cepat. Yang mana pun kamu bantah, di situ kriteria modul kamu mulai kelihatan.

---

© AlphaForge v2 — draft kerja, bukan spec
