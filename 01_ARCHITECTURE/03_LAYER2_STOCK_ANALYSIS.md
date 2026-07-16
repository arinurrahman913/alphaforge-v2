# Layer 2 — Stock Analysis Engine

**Status:** Aktif — revisi: pipeline dua fase + barrier dibuat eksplisit (D-03), kontrak data dirujuk
**Doc version:** 2.0.0

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

### 2.4 Barrier: Batas Fase A / Fase B

**Ini titik yang paling sering salah digambar.** Diagram sebelumnya menampilkan `Knowledge → [Confidence + Peer] → Risk` seolah alur lurus per ticker. Itu tidak mungkin: Peer/Relative Comparison butuh Knowledge **saham lain** sesektor. Selama Knowledge ticker pembanding belum jadi, Peer untuk ticker ini tidak bisa dihitung.

Jadi Layer 2 sebenarnya dua fase:

- **Fase A — per-ticker, paralel penuh:** Evidence → Knowledge. Tiap ticker berdiri sendiri, tanpa koordinasi. Dijamin oleh invariant di `03_LAYER2_SPECS/03_KNOWLEDGE.md`: Knowledge suatu ticker harus bisa dihitung dengan hanya Evidence ticker itu.
- **⟂ Barrier:** tunggu Knowledge seluruh kandidat selesai.
- **Fase B — butuh populasi:** Peer → Confidence → Risk → 3 modul → Aggregator.

Barrier ini bukan pilihan desain — ia konsekuensi tak terhindarkan dari memakai populasi hasil screening sendiri sebagai sumber peer. Yang bisa dipilih cuma: menggambarnya sekarang, atau menemukannya saat implementasi.

**Konsekuensi:** Fase A menghasilkan Knowledge untuk ~1.500–3.000 kandidat yang semuanya harus ada di tangan sebelum Fase B mulai. Ini satu-satunya titik di pipeline yang menahan populasi utuh — perlu masuk hitungan memori & waktu saat implementasi.

Untuk analisa ticker tunggal (mis. CLI `analyze AAPL`), tidak ada populasi — `peer_tickers` disuplai manual dan `peer_group_basis` ditandai `manual`, bukan `screening_universe`. Bedanya harus terlihat di output.

### 2.4b Confidence & Peer Comparison (Fase B)
- **Peer/Relative Comparison** — membandingkan saham dengan kompetitor/industri sejenis. Sejak D-03, ini **satu-satunya tempat angka relatif-peer hidup**; Knowledge sengaja dibuat murni per-ticker.
- **Confidence/Data Quality** — menandai seberapa kuat evidence yang mendasari knowledge ini, termasuk menerima `low_sample_size` & `peer_failures` dari Peer.

### 2.5 Risk / Red-Flag Check
Pemeriksaan yang berjalan sebelum Knowledge diteruskan ke modul reasoning. Kalau ditemukan red flag serius (masalah akuntansi, governance, litigasi besar), ini ditandai secara eksplisit dan **wajib direspons** oleh ketiga modul di bawah — bukan menghentikan proses secara default, dan bukan hanya jadi bagian dari salah satu modul saja. Kasus yang sudah terkonfirmasi sangat berat (fraud terbukti, delisting resmi) adalah pengecualian yang boleh menghentikan proses lebih awal — lihat Prinsip #4 di `00_Foundation/02_PRINCIPLES.md`.

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
FASE A — per-ticker, paralel penuh
  Screening → Evidence → Knowledge

═════════════════ BARRIER ═════════════════
  tunggu Knowledge SELURUH kandidat selesai

FASE B — butuh populasi
        Peer / Relative Comparison
                    ↓
          Confidence / Data Quality
                    ↓
            Risk / Red-Flag Check
                    ↓
    ┌───────────────┬──────────────────┬─────────────────┐
    ▼               ▼                  ▼
Multibagger    Quality/Compound     Speculative
                                   (+ Catalyst Tracking)
    └───────────────┴──────────────────┴─────────────────┘
                    ↓
              Aggregator  (3 pandangan, urutan tetap, tanpa verdict)
                    ↓
                 Output
                    ↓
      Historical Tracking / Decision Journal
```

Bentuk tiap paket yang mengalir di atas dikunci di `01_ARCHITECTURE/04_DATA_CONTRACTS.md`.

## 4. Kenapa Screening Market-Wide, Bukan Watchlist

v1 terbatas pada ~33 ticker tema tertentu (AI, Space, Energy) yang dipilih manual. Ini berarti peluang bagus di luar tema itu tidak pernah terlihat. v2 menyaring seluruh NASDAQ + NYSE, sehingga cakupannya tidak dibatasi bias pemilihan manual di awal.

---

© AlphaForge v2
