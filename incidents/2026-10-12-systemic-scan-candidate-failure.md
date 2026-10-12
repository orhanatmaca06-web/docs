# NEXORA BRAIN — 2026-10-12 Systemic Scan Candidate Failure

## This is systemic, not a KLRHO-only incident
The morning recommendation process elevated several candidates whose later price paths failed badly. Do not minimize this as routine volatility or use the result of one candidate to defend the whole process.

### Required source attribution
- Assistant-prioritized: KLRHO, ANELE, SMRTG, HATSN.
- User-discovered: KAPLM, ISGSY. Never record these as assistant scanner hits.
- Supplemental scan around 12:30 Türkiye time: CUSAN. Keep separate from the morning pool.
- RYSAS and RYGYO were mentioned as other scan names; judge them only with timestamp-aligned paths and their actual source labels.

### Incident values
User-reported intraday outcomes:
- KLRHO: approximately +7% then limit-down.
- ANELE: approximately +5% then limit-down.
- SMRTG: approximately +4% then -3%.
- HATSN: approximately +5% then -4%.

Later Fintables quotes around 15:40 Türkiye time were:
- KLRHO 13.58 TL (-4.63% daily)
- ANELE 19.65 TL (-9.53%)
- SMRTG 4.84 TL (-3.78%)
- HATSN 29.06 TL (+0.97%)
- KAPLM 143.60 TL (+2.79%)
- ISGSY 13.62 TL (+6.41%)
- CUSAN 20.72 TL (-4.87%)

These are different evidence types. The later snapshot does not independently prove the user's exact intraday +5% to -4% path. Keep the user-reported path and the later quote snapshot in separate fields and preserve source timestamps.

## Code-level causes confirmed in BTRSOMGA
1. The NEXORA first-pass StockSnapshot previously omitted required risk context: monthly/yearly change, intraday high and retracement, closed 5-minute OHLC, technical sell state, recent circuit-breaker count, explicit external-risk state and risk-context timestamp alignment.
2. Unknown multi-timeframe risk did not block score accumulation. This let a short-term positive percentage or score inputs mask a severe longer-term downtrend.
3. NEXORAScannerPipeline.discovery_eligible() previously accepted recovery_attempt OR breakout, allowing a recovery signal to pass despite an external negative-risk flag, and allowing a raw breakout to bypass the complete 5-minute Gate 3/3.
4. The BTRS core pipeline relied on a generic risk_block flag and did not separately validate multi-period drawdown, peak rejection, 5-minute bearish rejection, repeated circuit-breaker history and cross-source timestamp alignment before evaluating core eligibility.
5. The TradingView webhook normalization and row adapter were not carrying these risk fields, so merely adding a gate would have left missing evidence invisible at the source boundary.
6. GitHub's former .github/workflows/main.yml was not a valid Actions workflow; it contained only a one-line branch marker. CI reported failure with runner_id=0 and no steps. A real PR/push validation workflow has been added, but the new run still failed before any step (runner_id=0, steps=[]). This is an infrastructure blocker, not a test PASS.

## Permanent rule
**No candidate can be promoted or remain continuation-eligible based only on a positive daily return, score, or relative-volume value.**

Before discovery eligibility:
- Require source-backed monthly/yearly regime context.
- Require current price versus intraday high, a closed 5-minute OHLC candle, technical sell state, recent circuit-breaker count, external risk state and source timestamps to be present and aligned.
- Block repeated circuit-breaker risk, severe drawdown without an independently confirmed recovery setup, large intraday peak rejection, bearish 5-minute rejection, negative-day weakness without recovery, and any external negative risk.
- If data is missing, stale, delayed or timestamp-misaligned, emit INSUFFICIENT_EVIDENCE/WATCH/REJECT, not ENTRY.
- A recovery setup may be retained as RADAR/WATCH when source-backed and no external negative risk is present; it is not by itself an ENTRY.
- Trend continuation requires closed-5-minute confirmation plus breakout, retest and existing BTRS Gate 3/3 evidence.
- Delayed volume may never satisfy LIVE_VOLUME.
- Every candidate result must preserve candidate_origin, source, source_timestamp_utc, captured_at_utc, data_delay_seconds, risk_context_timestamp_utc, risk_flags, decision, reasons and immutable evidence ID.

## Fix status
BTRSOMGA PR #166: https://github.com/orhanatmaca06-web/BTRSOMGA/pull/166

The code patch and regression tests are on a review branch. It is not merged, deployed or accepted. Current CI evidence is blocked before steps start because runner_id=0 and steps=[]. The provider adapter must populate the new risk fields from real timestamped sources; then tests/CI, runtime and same-symbol live evidence must pass before closure.
