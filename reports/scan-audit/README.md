# BIST current quote evidence snapshot — 2026-10-12

- Captured for the scan-error audit at approximately 14:43 Europe/Istanbul.
- Provider: Fintables `hisse_senetleri` quote table.
- Fields: symbol, current price, quote source timestamp (UTC), current daily percentage.
- Query was split into three alphabetical read-only ranges to remain within provider result limits.
- Some rows have null prices/timestamps or stale source timestamps; those are not valid fresh quote confirmations.
- Current source snapshot file: `bist-current-quotes-20261012.csv`.
