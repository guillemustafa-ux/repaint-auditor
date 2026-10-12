# Repaint Auditor

Open-source TradingView indicator (Pine Script v6) that measures repainting instead of guessing it.

## What it measures

1. **Live flicker counter.** Pick any other indicator's plot as the source. While the chart runs in realtime, the script observes it tick by tick and counts:
   - bars with at least one intrabar change, and total intrabar changes;
   - ghost signals: the plot was "on" (not na, not zero) at some tick of the bar but "off" at the bar close;
   - signals still on at the bar close.
2. **request.security A/B.** The same higher-timeframe value is requested in three forms and compared:
   - `[1]` + `lookahead_on` (the documented non-repainting form);
   - `lookahead_off` (the default);
   - `lookahead_on` without `[1]` (returns future data on historical bars).

   The history column shows on how many past bars the form without `[1]` already held a future value. The live column counts intrabar changes of each form. The `[1]` + `lookahead_on` form should stay at 0 live changes when the chart timeframe divides the higher timeframe evenly (for example 1m and 60m).

The A/B section uses the script's own OHLC (chosen with a string input), not the external source, because a `request.*()` call that receives another script's plot evaluates it with the chart symbol's data, not the requested symbol's.

## Files

- `repaint_auditor.pine`: the indicator.
- `tests/flicker_source.pine`: helper that plots 1 while the bar is green and 0 otherwise. A bar that trades above its open and closes at or below it produces a ghost signal.
- `TRADINGVIEW_PUBLICATION.md`: description text and checklist for publishing.
- `LICENSE`: MIT.

## Live test

1. Open MNQ1! (or any liquid instrument with live data) on the 1-minute chart during market hours.
2. Add `tests/flicker_source.pine` from the Pine Editor, then add the auditor and set "Plot to audit" to "Flicker source: Green bar flag". Keep "Higher timeframe" = 60.
3. Wait at least 10 full 1-minute bars.
4. Expected:
   - "[1] + lookahead_on" live changes = 0;
   - "lookahead_off" and "lookahead_on, no [1]" live changes > 0;
   - with the helper, ghost signals are usually > 0, with red "ghost" marks on those bars;
   - the history column shows the leak on at least ~95% of bars for 1m vs 60m.
5. Reload the chart. The ghost marks and live counts disappear, because those bars are now historical and only their final value remains. That disappearance is the repaint being demonstrated.

## Limitations

- Live counts only cover bars observed tick by tick since the chart was loaded. The bar in progress at load time is skipped. Reloading clears them (the state uses `varip`, which is not preserved across reloads).
- History has no tick data: intrabar behavior of a plot cannot be reconstructed from past bars.
- The count depends on the data feed's tick delivery; a slow or batched feed shows fewer intermediate changes.
- The "future value on at least L of M bars" figure is a lower bound: equal values by coincidence are not counted.
- It audits the value of a plot, not the logic behind it, and it cannot see repainting done only through drawing objects.
- The script has not been compiled outside TradingView; compile it in the Pine Editor before relying on it.

## Related

`ict-sweep-mss-fvg`, `gex-levels-proof`.

## License

MIT. See `LICENSE`.
