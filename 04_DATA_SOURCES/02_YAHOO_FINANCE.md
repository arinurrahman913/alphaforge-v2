# Yahoo Finance

**Status:** Aktif

## Cakupan

Sumber data utama untuk harga, data fundamental dasar, dan beberapa indeks pasar (VIX, DXY, commodity futures, sector ETF). Sudah dipakai di v1 lewat library `yfinance`.

## Limitasi Penting

- **Bukan API resmi** — `yfinance` bekerja dengan scraping, bukan endpoint resmi Yahoo. Ini berarti ada risiko perubahan struktur data sewaktu-waktu tanpa pemberitahuan.
- **Tidak punya endpoint "daftar semua ticker NASDAQ/NYSE".** Untuk kebutuhan Screening market-wide, daftar ticker harus didapat dari sumber lain (`03_MARKET_LISTING_SOURCES.md`), baru detailnya ditarik dari Yahoo Finance satu per satu.
- **Rentan rate-limit** kalau memanggil banyak ticker sekaligus dalam waktu singkat — krusial untuk diperhatikan mengingat scope screening di v2 jauh lebih luas dari watchlist manual v1. Lihat `05_RATE_LIMIT_CACHING_STRATEGY.md`.

## Dipakai Untuk

- Harga & data fundamental per saham (Evidence, Layer 2)
- VIX (`^VIX`)
- DXY (`DX-Y.NYB`)
- Commodity futures (`GC=F`, `CL=F`)
- Sector ETF (XLK, XLE, XLF, dst — untuk Sector Rotation & Money Flow proxy)
- Index utama (S&P 500, Nasdaq Composite — untuk Market Regime & Breadth)

---

© AlphaForge v2
