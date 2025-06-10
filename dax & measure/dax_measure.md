**Spotify Power BI – DAX Measures**


📊 KPI Metrics
▶️ Count Metrics

Count of Album = COUNT(spotify_history[Album Name])

Count of Artist = COUNT(spotify_history[Artist Name])

Count of Track = COUNT(spotify_history[Track Name])

===============================================================
===============================================================

▶️ **Distinct Count Metrics**

No. of Albums = DISTINCTCOUNT(spotify_history[Album Name])

No. of Artist = DISTINCTCOUNT(spotify_history[Artist Name])

No. of Track = DISTINCTCOUNT(spotify_history[Track Name])

===============================================================
===============================================================

📈 **Year-Wise Calculations**
▶️ Maximum and Minimum Year


Max Year = MAX(dimDate[Year])
▶️ Latest Year Distinct Counts

LatestYearAlbum = 
VAR _LatestYear = MAX(dimDate[Year])
RETURN CALCULATE(DISTINCTCOUNT(spotify_history[Album Name]), dimDate[Year] = _LatestYear)

LatestYearArtist = 
VAR _LatestYear = MAX(dimDate[Year])
RETURN CALCULATE(DISTINCTCOUNT(spotify_history[Artist Name]), dimDate[Year] = _LatestYear)

LatestYearTrack = 
VAR _LatestYear = MAX(dimDate[Year])
RETURN CALCULATE(DISTINCTCOUNT(spotify_history[Track Name]), dimDate[Year] = _LatestYear)

===============================================================
===============================================================

▶️**Previous Year Distinct Counts**

PreviousYearAlbum = 
VAR _LatestYear = MAX(dimDate[Year])
VAR _PreviousYear = _LatestYear - 1
RETURN CALCULATE(DISTINCTCOUNT(spotify_history[Album Name]), dimDate[Year] = _PreviousYear)

PreviousYearArtist = 
VAR _LatestYear = MAX(dimDate[Year])
VAR _PreviousYear = _LatestYear - 1
RETURN CALCULATE(DISTINCTCOUNT(spotify_history[Artist Name]), dimDate[Year] = _PreviousYear)

PreviousYearTrack = 
VAR _LatestYear = MAX(dimDate[Year])
VAR _PreviousYear = _LatestYear - 1
RETURN CALCULATE(DISTINCTCOUNT(spotify_history[Track Name]), dimDate[Year] = _PreviousYear)

===============================================================
===============================================================

▶️ **PY and YOY KPI Measures**

PY and YOY Album KPI =
VAR _Latest = [LatestYearAlbum]
VAR _Previous = [PreviousYearAlbum]
VAR _YOY = IF(NOT(ISBLANK(_Previous)), DIVIDE(_Latest - _Previous, _Previous, 0), BLANK())
RETURN
    IF(NOT(ISBLANK(_Previous)),
        "vs PY: " & FORMAT(_Previous, "#,##0") & " (" & FORMAT(_YOY, "0.00") & ")",
        "No Data")


PY and YOY Artist KPI =
VAR _Latest = [LatestYearArtist]
VAR _Previous = [PreviousYearArtist]
VAR _YOY = IF(NOT(ISBLANK(_Previous)), DIVIDE(_Latest - _Previous, _Previous, 0), BLANK())
RETURN
    IF(NOT(ISBLANK(_Previous)),
        "vs PY: " & FORMAT(_Previous, "#,##0") & " (" & FORMAT(_YOY, "0.00") & ")",
        "No Data")


PY and YOY Track KPI =
VAR _Latest = [LatestYearTrack]
VAR _Previous = [PreviousYearTrack]
VAR _YOY = IF(NOT(ISBLANK(_Previous)), DIVIDE(_Latest - _Previous, _Previous, 0), BLANK())
RETURN
    IF(NOT(ISBLANK(_Previous)),
        "vs PY: " & FORMAT(_Previous, "#,##0") & " (" & FORMAT(_YOY, "0.00") & ")",
        "No Data")

===============================================================
===============================================================

📉 **Line Chart Annotations (Min/Max Highlights)**

Min_Max Album Line Chart =
VAR _MaxValue = MAXX(ALLSELECTED(dimDate[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[Album Name])))
VAR _MinValue = MINX(ALLSELECTED(dimDate[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[Album Name])))
VAR _CurrentValue = DISTINCTCOUNT(spotify_history[Album Name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())


Min_Max Artist Line Chart =
VAR _MaxValue = MAXX(ALLSELECTED(dimDate[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[Artist Name])))
VAR _MinValue = MINX(ALLSELECTED(dimDate[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[Artist Name])))
VAR _CurrentValue = DISTINCTCOUNT(spotify_history[Artist Name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())


Min_Max Track Line Chart =
VAR _MaxValue = MAXX(ALLSELECTED(dimDate[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[Track Name])))
VAR _MinValue = MINX(ALLSELECTED(dimDate[Year]), CALCULATE(DISTINCTCOUNT(spotify_history[Track Name])))
VAR _CurrentValue = DISTINCTCOUNT(spotify_history[Track Name])
RETURN
    IF(_CurrentValue = _MaxValue || _CurrentValue = _MinValue, _CurrentValue, BLANK())
    
===============================================================
===============================================================

⏱️ **Listening Time & Engagement**

Avg Listening Time (min) = 
AVERAGE(spotify_history[ms_played]) / 60000

===============================================================
===============================================================

🔄 **CF Quadrant Logic (Cluster Framework)**

CF Quadrant = 
VAR AvgTime = [Avg Listening Time (min)] <= 'Listening Time (min)'[Listening Time (min) Value]
VAR TrackFreq = [Track Frequency] <= 'Track Frequency Parameter'[Track Frequency Parameter Value]

VAR Result = 
    SWITCH(TRUE(),
        AvgTime && TrackFreq, 1,              // Low Time & High Freq
        NOT AvgTime && TrackFreq, 2,          // High Time & High Freq
        NOT AvgTime && NOT TrackFreq, 3,      // High Time & Low Freq
        AvgTime && NOT TrackFreq, 4           // Low Time & Low Freq
    )

RETURN Result
🔁 Track Level Frequency
dax
Copy
Edit
Track Frequency = COUNTROWS(spotify_history)

===============================================================
===============================================================
