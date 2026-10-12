# NEXORA Brain Rule: Early-Momentum Failure Gate

**Incident date:** 2026-10-12 (Türkiye)
**Incident symbol:** KLRHO
**Status:** Recorded rule; implementation/enforcement is not yet proven.

## Root cause
The morning scan elevated KLRHO without sufficiently weighting the severe longer-term downtrend, a sharp intraday rejection from the high, and repeated circuit-breaker event risk. The failure was candidate qualification, not simply failure to find a high-activity symbol.

## Permanent decision rules
- Relative volume is contextual only. It is never a standalone qualification or override.
- A positive daily/weekly return cannot override severe monthly/yearly weakness or a failed higher-timeframe regime gate.
- A sharp 5-minute rejection after an intraday high invalidates continuation qualification until a fresh setup is independently confirmed.
- KAP circuit-breaker notices and repeated event flags must be incorporated into candidate risk and priority; they are risk evidence, not proof of causation.
- Delayed or ambiguous volume data cannot satisfy a live-volume gate. Record the 15-minute delay explicitly.
- Keep scan-time evidence immutable. Never use later outcomes to pretend they were known at scan time.
- Missing, stale, conflicting, or delayed required evidence results in WATCH/REJECT/INSUFFICIENT_EVIDENCE, never ENTRY.
- Scan discovery and trade entry are separate. Existing BTRS Core and confirmation gates remain authoritative; this rule does not create new engines or thresholds.

## KLRHO evidence recorded
- Supplied scan snapshot at approximately 11:34 Türkiye: 14.62 TL, +2.67% daily; monthly field -57.69%, yearly field -81.12%.
- 5-minute bar beginning 10:50 Türkiye: O 15.35, H 15.41, L 14.55, C 14.67.
- Fintables quote timestamp 2026-10-12T09:50:00Z: 13.86 TL, -2.67%.
- KAP pay-based circuit-breaker notice published 2026-10-12; prior KLRHO notices were also present on 2026-10-08.
- The Fintables OHLCV volume fields are 15-minute delayed and must not be represented as live.

## Implementation acceptance
Implement deterministic tests for severe longer-term weakness hidden by positive daily return, intraday rejection, repeated circuit-breaker events, delayed volume mislabeled as live, and timestamp mismatch. Verify failing-before/passing-after tests, CI, runtime, and real-data evidence before claiming production enforcement.

**Do not report PASS or production acceptance from this document alone.**
