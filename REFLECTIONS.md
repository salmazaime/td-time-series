# Reflections — TD4 Time series

> Lines marked **[YOUR OBSERVATION]** need your own numbers from your dashboard / Actions output. Reword everything in your own voice.

## Stage 0 — Reflection 1
I could have checked the API documentation, or just made one real test call with `past_days=90` and looked at how many rows came back. A thirty-second request would have shown that real history exists, so the synthetic-data design was built on an unverified assumption. An assumption about what data is available is itself a claim that needs checking.

## Stage 1 — Reflection 2 (prediction, written before running)
I expect CO2 in Le Mans to be **rising** over the last 90 days. The window covers late summer into autumn: plants stop absorbing as much CO2 once the growing season ends and decomposition continues, so the Northern Hemisphere concentration climbs from its late-summer minimum towards its winter peak.
**[YOUR OBSERVATION]** My actual trend slope was ____ ppm/day, so the prediction was (right / wrong / partly right because ____).

## Stage 2 — Reflection 3
No, zero gaps does not make the gap detection unnecessary. This run was clean because the source is a reanalysis model that backfills every hour, but "clean this one time" is not "guaranteed always clean". The API could return nulls, the model could change, or I could point the pipeline at a raw sensor feed that does drop readings. Without the check, a missing day would silently distort the trend; with it, the gap is reported. The code costs almost nothing and protects against a failure nobody is watching for.

## Stage 3 — Reflection 4
- **Rolling mean:** better when I want a stable, easy-to-explain value and a sudden spike is probably noise (for example a one-day sensor glitch), because a single outlier is spread over 7 days and then drops out completely.
- **Exponential smoothing:** better when recent values matter more and I want to react faster to a real change, because the latest points carry the most weight (alpha = 0.3). On a slow steady trend it follows the data more closely than the rolling mean, which lags behind.
**[YOUR OBSERVATION]** In my output the last rolling-mean value was ____ and the last exp-smoothed value was ____, vs. the last raw value ____.

## Stage 4 — Reflection 5
If all 4 retries fail, `fetch_series.py` raises a `RuntimeError`, so the job stops at that step. The later steps (`analyze_series.py` and the commit/push step) never run. Nothing is overwritten: `data/series.csv` and `docs/data/series_analysis.json` keep last week's values and the dashboard still loads. The data goes stale rather than breaking, but nothing on the dashboard says so apart from the old `generated_at` timestamp, and the only alert is the red run in the Actions tab.

## Stage 5 — Reflection 6
I would trust the projection for about **3 to 5 days**, not more than the 7 shown. The line is a straight fit through 90 days with a slope of ____ ppm/day **[YOUR OBSERVATION]**; over a few days the CO2 level really does change slowly, so a straight line is a reasonable guess. But CO2 follows a yearly cycle and the daily values are noisy, so the error grows with every day, and a much longer extrapolation (weeks) would cross into a different part of the seasonal cycle where the slope itself changes. Much shorter than 3 days would be fine too but adds nothing over just reading the latest value.

## Stage 6 — Reflection 7
Extension I did: **[YOUR OBSERVATION: new location (name, lat/lon) or new variable]**; result: ____ ppm/day vs. Le Mans ____ ppm/day.
The pipeline kept working because `clean_series`, `rolling_mean`, `exponential_smoothing` and `linear_trend_projection` never mention CO2, ppm, Le Mans or any location. They only work with generic (day, value) pairs; `load_series` and `aggregate_to_daily` just read a column of numbers. Only the fetch parameters (coordinates, variable name, unit) are specific to the data, so changing those is enough.
