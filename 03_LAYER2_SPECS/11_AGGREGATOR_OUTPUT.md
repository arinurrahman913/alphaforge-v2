# Aggregator & Output

**Status:** Aktif

## Definisi

Komponen akhir Layer 2 yang menyusun hasil dari ketiga modul reasoning (Multibagger, Quality/Compound, Speculative) untuk ditampilkan berdampingan.

## Kenapa Penting

Ini adalah implementasi langsung dari Prinsip #3 (Multi-Lens, Bukan Single-Verdict) — Aggregator sengaja tidak diberi wewenang menggabungkan tiga pandangan jadi satu angka.

## Sumber Data / Pendekatan

Menerima output dari ketiga modul reasoning, beserta Confidence Score dan flag dari Risk/Red-Flag Check.

## Cara Kerja

Susun ketiga hasil reasoning berdampingan tanpa mengubah kesimpulan masing-masing modul. Sertakan Confidence Score dan Risk flag secara eksplisit di output, bukan disembunyikan.

## Terhubung Dengan

Menerima input dari ketiga modul reasoning. Outputnya diteruskan ke Historical Tracking / Decision Journal.

---

© AlphaForge v2
