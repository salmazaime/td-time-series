# Reflections TD4

## Stage 0, Reflection 1
I could have made one test call to the API with past_days=90 and looked at what came back, or read its documentation. It would have taken a minute and shown that real history exists. We designed around an assumption nobody had checked.

## Stage 1, Reflection 2
I expected CO2 to be rising, because the last 90 days go from late summer to autumn, when plants stop absorbing CO2 and the level climbs toward the winter peak. My result agrees. For Reykjavik the trend is +0.1531 ppm per day and the series goes from about 440 to about 464 ppm.

## Stage 2, Reflection 3
No, I would keep the gap detection. My run had zero gaps, but that only means this source was clean this time. The data comes from a model that fills every hour, and a real sensor or a future change in the API could still leave missing days. Without the check they would quietly distort the trend.

## Stage 3, Reflection 4
At the end of my chart the exponential smoothing (about 459) is higher than the rolling mean (about 455) because it reacted faster to the jump in early November. I would use the rolling mean when a sudden spike is probably just noise, since it spreads it over 7 days. I would use exponential smoothing when I want to catch a real change early.

## Stage 4, Reflection 5
If the API call fails four times, the fetch step crashes and the job stops there. The analysis and commit steps never run, so the two data files keep last week's values. The dashboard still works but shows old data, and only the red run in Actions and the old timestamp tell you something went wrong.

## Stage 5, Reflection 6
I would trust the projection for about 3 days, not the 7 shown. My line starts around 449 ppm while the latest reading is 463.6, so it clearly ignores the recent jump, and the daily values swing by 10 ppm or more. Over a few days CO2 changes slowly, but after that the error grows and over weeks the seasonal cycle changes the slope itself.

## Stage 6, Reflection 7
I changed the location from Le Mans to Reykjavik and nothing else broke. The cleaning, smoothing and projection functions never mention CO2, ppm or any place. They only take days and values, so only the fetch settings need to change.
