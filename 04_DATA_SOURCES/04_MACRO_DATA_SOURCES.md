# Macro Data Sources

**Status:** Aktif

## Kebutuhan

Sebagian besar komponen Layer 1 (Business Cycle Stage, Liquidity Conditions, Yield Curve, Macro Calendar) butuh data makroekonomi resmi yang tidak tersedia lewat Yahoo Finance.

## Sumber Utama: FRED (Federal Reserve Economic Data)

Gratis, resmi, dikelola oleh Federal Reserve Bank of St. Louis. Menyediakan:

- **Yield curve** — data suku bunga treasury berbagai tenor
- **GDP, ISM PMI, unemployment rate** — untuk Business Cycle Stage
- **Fed balance sheet, M2 money supply** — untuk Liquidity Conditions
- **Release calendar** — jadwal rilis data ekonomi resmi, untuk Macro Calendar

## Sumber Pelengkap

- **AAII Investor Sentiment Survey** — data sentimen mingguan, gratis, dipakai untuk Market Sentiment.
- **CBOE Put/Call Ratio** — gratis (delayed), dipakai untuk Market Sentiment.

## Catatan Implementasi

FRED punya API resmi (perlu API key gratis, bukan berbayar). Ini beda dari Yahoo Finance yang berbasis scraping — jadi secara keandalan, FRED lebih stabil untuk dipakai jangka panjang.

Untuk versi "kalender ekonomi" yang lebih enak dibaca (seperti tampilan di Investing.com), sebaiknya dihindari kalau caranya scraping situs pihak ketiga — cukup pakai release calendar resmi dari FRED meski tampilannya lebih sederhana.

---

© AlphaForge v2
