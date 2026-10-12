# Repaint Auditor: Live Flicker Counter and request.security A/B

Description to paste as is (between the rules):

---
WHAT IT DOES
Repaint Auditor measures whether a plot changes while a bar is still forming. You pick any other indicator's plot as the source. While the chart runs in realtime, the script observes that plot on every tick and counts how many bars had at least one intrabar change, how many changes happened in total, and how many "ghost" signals appeared. A ghost signal is a bar where the plot was on (not na and not zero) at some tick but was off when the bar closed.

It also runs a second check on request.security. The same higher timeframe value is requested in three forms, and the script compares them on history and live.

WHAT IS ORIGINAL HERE
Most repaint checks are visual: you watch a signal and decide by eye. This script turns that into counts. Two ideas are combined. First, a tick-by-tick audit window built with varip state that skips the bar in progress when the chart loads, because that bar was not observed from its first tick. Second, a side by side comparison of three request.security forms: [1] with lookahead_on, lookahead_off, and lookahead_on without the offset. The history column counts bars where the forms disagree, and the live column counts how often each form changes while the higher timeframe bar develops.

The comparison uses the script's own open, high, low or close, selected by an input. It does not feed the external source into request.security, because a request that receives another script's plot evaluates that series on the chart symbol, not on the requested context.

HOW TO USE IT
1. Add the indicator to a chart with live data, for example a liquid futures contract on the 1 minute timeframe during market hours.
2. Set "Plot to audit" to the plot of the indicator you want to test. Non-zero, non-na values count as on.
3. Optionally set "Ignore intrabar changes up to (ticks)" to filter out tiny moves.
4. Choose a higher timeframe above the chart timeframe for the request.security section.
5. Let at least 10 full bars finish and read the table. Reloading the chart clears the live counts.

HOW TO READ THE TABLE
Live bars audited: bars that were fully observed in realtime since load. Ticks: executions counted inside the audit window.
Your plot: bars with intrabar changes (k of N), total intrabar changes, ghost signals, and signals that were still on at the bar close.
request.security A/B: one row per form. The History column describes what each form shows on past bars. For the form without the offset it reports on how many bars it already showed a value that the lookahead_off form did not have yet, as "at least" because equal values by coincidence are not counted. The Live column counts intrabar changes of each form.
Expected behavior from the documentation: the [1] with lookahead_on form changes only when a new higher timeframe period starts, which is always a new chart bar, so its live count should stay at 0. The other two forms can change while the higher timeframe bar develops.

WHAT IT CANNOT SEE
Historical bars carry no tick data, so the intrabar behavior of a plot on past bars cannot be reconstructed. After a reload, bars that were realtime become historical and only their final value remains; the ghost marks and live counts disappear. That disappearance is the repaint being shown. The script also cannot judge the logic behind a plot, and it does not detect repainting done only through drawing objects.

LIMITATIONS
Live counts depend on how many ticks the data feed delivers; a batched feed can show fewer intermediate changes. The state uses varip, which behaves like var on historical bars and is kept across ticks only on realtime bars, so results can differ after a reload. The history figure for the no-offset form is a lower bound. The script reports counts, not a verdict on whether a strategy is good or bad, and it is not financial advice.

The chart shows [SYMBOL] on the [TF] timeframe with the default inputs.
---

## Publishing checklist

- [ ] Compile `repaint_auditor.pine` in the Pine Editor with no errors or warnings.
- [ ] Run the live test from `README.md` and confirm the expected results.
- [ ] Set the chart: MNQ1! (or similar), 1-minute, default inputs, helper added so ghost marks are visible.
- [ ] Replace `[SYMBOL]` and `[TF]` in the last line with the real values of the chart.
- [ ] Publish as open source, with the MIT license text if the form allows it.
- [ ] Check that the description contains no links, contact info, or performance claims.
- [ ] Choose the category and tags (for example: repainting, request.security, lookahead).
- [ ] Add the TradingView script URL to `README.md` after publishing.
