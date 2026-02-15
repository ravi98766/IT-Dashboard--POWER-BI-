# Service Desk Performance Insights – SLA vs Resolution Dynamics
---------------------------------------------------------------------------------------------------------
# Dashboard 1 – High-Level Operational Overview
<img width="894" height="506" alt="D1" src="https://github.com/user-attachments/assets/a69338c9-f0a9-4edf-a699-c3d06b5c6c45" />

This screen functions as an executive summary of ticket performance.

Top KPI Tiles

•	Total Tickets → 97K

Overall ticket volume across the dataset.

•	Average Resolution Time → 4.55

Mean time required to close tickets (likely measured in hours or days depending on the model).

•	SLA Compliance % → 48.22%

Portion of tickets resolved within SLA targets.

•	SLA Non-Compliance % → 51.78%

Portion breaching SLA thresholds.

**Observation: Non-compliance slightly exceeds compliance, indicating SLA risk.**

**Heatmaps (Resolution Behaviour Patterns)**

1.	Avg. Resolution Time by Age & Year

o	Rows → Agent age

o	Columns → Year (2016–2020)

o	Darker shades → Higher resolution time

➡ Used to detect whether certain age bands show slower resolution trends over time.

2.	Avg. Resolution Time by Age & Month

o	Rows → Age

o	Columns → Month

➡ Reveals seasonality effects or age-related productivity patterns.

3.	Avg. Resolution Time by Month & Year

o	Tracks monthly variations across years.

➡ Useful for workload spikes, policy shifts, or operational anomalies.

Filter by Age Segment

Segments such as:

•	Old Agents

•	Other Agents

•	Youthful Agents

➡ Enables cohort-based performance comparison
___________________________________________________________________________________________________________________________________________________________
# Dashboard 2 – SLA Non-Compliance Analysis
<img width="905" height="500" alt="D2" src="https://github.com/user-attachments/assets/703b7f0a-0eef-44cc-bc0e-36d4da2afa12" />

Focuses on SLA violations and temporal movement.

Primary Metric

•	52.29% Monthly SLA Non-Compliance Trend

➡ Indicates more than half of tickets breach SLA in the selected year.

Trend Chart

Monthly non-compliance variation (Jan–Dec).

➡ Helps identify problematic months or improvement cycles.

Summary Tiles

•	Total Tickets → 29K

•	Avg Resolution Time → 4.59

•	Total Agents → 50

•	Avg Agent Age → 41.40

➡ Contextual workforce metrics.

SLA Change Panels

Non-Compliance SLA Tickets

•	Volume → 15K

•	YoY Change → 38.06%

Compliance SLA Tickets

•	Volume → 14K

•	YoY Change → 32.52%

➡ Interpretation: SLA issues are rising faster than compliant resolutions.

SLA Non-Compliance by Priority

Breakdown across:

•	Low

•	Unassigned

•	Mid

•	High

➡ Determines whether SLA breaches correlate with ticket criticality.

_________________________________________________________________________________________________________________________________________________________________

# Dashboard 3 – Ticket Composition & Satisfaction
<img width="906" height="499" alt="D3" src="https://github.com/user-attachments/assets/c6403d01-90fa-42d7-85cd-79af8ec04204" />

Provides categorical and behavioural insights.

Total Tickets by Request Category

Example distribution:

•	System → Highest volume

•	Login Access

•	Software

•	Hardware → Lowest

➡ Identifies dominant workload drivers.

Total Tickets by Priority

•	High → Largest share

•	Unassigned

•	Low

•	Mid

➡ Priority skew analysis for workload management.

Agent Performance Tiles

•	Satisfaction (Avg) → 4.10

•	Total Agents → 50

•	Average Resolution Time → 4.55

•	Average Agent Age → 41.40

➡ Workforce effectiveness snapshot.

Issue Type Distribution

•	IT Request → 75%

•	IT Error → 25%

➡ Indicates service vs incident mix.

Monthly & Yearly Ticket Trend

Time-series growth from 2016–2020.

➡ Reveals long-term demand escalation.
_____________________________________________________________________________________________________________________________________________________________________________
# Tooltip 1 – Monthly Ticket & Satisfaction Snapshot
<img width="363" height="456" alt="Tooltip 1" src="https://github.com/user-attachments/assets/434aa2be-f447-4891-9d38-3aa1a40ab820" />

Contextual drill-down view.

•	Month & Year → Jan 2016

•	Total Tickets → 97K

•	Average Resolution Time → 4.55

Satisfaction Labels

•	Very Satisfied → 51K

•	Satisfied → 28K

•	Very Dissatisfied → 10K

•	Neutral → 7K

•	Dissatisfied → 2K

➡ Overall sentiment heavily positive despite SLA challenges.
______________________________________________________________________________________________________________________________________________________________
# Tooltip 2 – Hardware SLA Status
<img width="255" height="232" alt="Tooltip 2" src="https://github.com/user-attachments/assets/b06fce9c-f5d5-443c-906a-dcdad108b177" />

Category-specific SLA view.

•	Hardware Tickets → 97K

•	Within SLA → 47K

•	Outside SLA → 50K

➡ Slight SLA breach dominance.

________________________________________
# Tooltip 3 – High Priority SLA & Satisfaction

Priority-focused diagnostic.

•	Priority → High

•	Avg Resolution Time → 4.55

•	Avg Satisfaction Rate → 4.10

•	Within SLA → 47K

•	Outside SLA → 50K

➡ High priority items still experience major SLA breaches — operational concern.

____________________________________________________________________________________________________________________________________________________________________

# DAX AND MEASURES: 
```
% of IT Error = DIVIDE([Total IT Error Ticket], [Total Tickets])
```
```
% of IT Request = DIVIDE([Total IT Request Ticket], [Total Tickets])
```
```
Average Agent Age = 
AVERAGE(IT_Agents[Age])
```
```
Avg Resolution Time = AVERAGE(Tickets[Resolution Time (Days)])
```
```
Avg satisfaction rate = AVERAGE(Tickets[Satisfaction Rate])
```
```
Compliance SLA = 
CALCULATE(
    COUNTROWS(FILTER(Tickets, Tickets[Resolution Status] = "Within SLA"))
)
```
```
Compliance SLA Commentary = 
VAR _SelectedYear = SELECTEDVALUE(Calendar[Year])
VAR _Pct_of_Compliance_SLA = [Compliance SLA YoY %]
VAR _MaxYear = MAX('Calendar'[Date])
VAR _backByOneyear = CALCULATE(MAX('Calendar'[Date]), 'Calendar'[Date] = _MaxYear - 1)
VAR _Result = IF(_backByOneyear <> BLANK(), _backByOneyear, "")
VAR _Previous_ComplianceYoYChange =
    CALCULATE(
        [Compliance SLA],
        SAMEPERIODLASTYEAR(Calendar[Date])
    )
VAR _Current_ComplianceSLA = [Compliance SLA]

VAR _Result2 =
    _Current_ComplianceSLA - _Previous_ComplianceYoYChange

RETURN
IF(
    _Previous_ComplianceYoYChange <> BLANK(),
    FORMAT(_Result2, "#,###") & " is the total compliance SLA changes between " & _SelectedYear & " and " & _backByOneyear & " which is "
        & FORMAT(_Pct_of_Compliance_SLA, "0.00 % ") & " YoY Change. ",
    _SelectedYear & " has no previous, select another year to see the result. "
)
```
```
Compliance SLA YoY % = 
VAR _Current_Compliance_SLA = [Compliance SLA]

VAR _Previous_Compliance_SLA =
    CALCULATE(
        [Compliance SLA],
        DATEADD(Calendar[Date], -1, YEAR)
    )

VAR _Result =
    IF(
        ISBLANK(_Current_Compliance_SLA) ||
        ISBLANK(_Previous_Compliance_SLA),
        BLANK(),
        DIVIDE(
            _Current_Compliance_SLA - _Previous_Compliance_SLA,
            _Previous_Compliance_SLA,
            0
        )
    )

RETURN
    IF(_Result <> BLANK(), _Result, 0)
```
```
Non Compliance SLA = 
CALCULATE(
    COUNTROWS(FILTER(Tickets, Tickets[Resolution Status] = "Outside SLA"))
)
```
```
Non Compliance SLA Commentary = 

VAR _SelectedYear = SELECTEDVALUE(Calendar[Year])
VAR _Pct_of_Compliance_SLA = [Non Compliance SLA YoY %]
VAR _MaxYear = MAX('Calendar'[Date])
VAR _backByOneyear = CALCULATE(MAX('Calendar'[Date]), 'Calendar'[Date] = _MaxYear - 1)
VAR _Result = IF(_backByOneyear <> BLANK(), _backByOneyear, "")
VAR _Previous_ComplianceYoYChange =
    CALCULATE(
        [Non Compliance SLA],
        SAMEPERIODLASTYEAR(Calendar[Date])
    )
VAR _Current_ComplianceSLA = [Non Compliance SLA]

VAR _Result2 =
    _Current_ComplianceSLA - _Previous_ComplianceYoYChange

RETURN
IF(
    _Previous_ComplianceYoYChange <> BLANK(),
    FORMAT(_Result2, "#,###") & " is the total compliance SLA changes between " & _SelectedYear & " and " & _backByOneyear & " which is "
        & FORMAT(_Pct_of_Compliance_SLA, "0.00 % ") & " YoY Change. ",
    _SelectedYear & " has no previous, select another year to see the result. "
)
```
```
Non Compliance SLA YoY % = 

VAR Non_Current_Compliance_SLA = [Non Compliance SLA]

VAR _Previous_Non_Compliance_SLA =
    CALCULATE(
        [Non Compliance SLA],
        DATEADD(Calendar[Date], -1, YEAR)
    )

VAR _Result =
    IF(
        ISBLANK(Non_Current_Compliance_SLA) ||
        ISBLANK(_Previous_Non_Compliance_SLA),
        BLANK(),
        DIVIDE(
            Non_Current_Compliance_SLA - _Previous_Non_Compliance_SLA,
            _Previous_Non_Compliance_SLA,
            0
        )
    )

RETURN
    IF(_Result <> BLANK(), _Result, 0)
```
```
SLA Compliance(%) = DIVIDE(COUNTROWS(FILTER(Tickets,Tickets[Resolution Status]="Within SLA")),[Total Tickets])
```
```
SLA Non-Compliance(%) = DIVIDE(CALCULATE([Total Tickets],Tickets[Resolution Status]="Outside SLA"),[Total Tickets])
```
```
Title Month = 
 VAR _selectedmeasure =SELECTEDVALUE('Switch measure'[Switch measure Order])
 RETURN
 IF(_selectedmeasure=0,"Monthly SLA Compliance Trend ","Monthly SLA Non Compliance Trend")
Title Priority = 
 VAR _selectedmeasure =SELECTEDVALUE('Switch measure'[Switch measure Order])
 RETURN
 IF(_selectedmeasure=0,"SLA Compliance by Priority ","SLA Non Compliance by Priority")
```
```
Total Agents = 
DISTINCTCOUNT(Tickets[Agent ID])
```
```
Total IT Error Ticket = CALCULATE([Total Tickets],Tickets[Issue Type]="IT Error") 
```
```
Total IT Request Ticket = CALCULATE([Total Tickets],Tickets[Issue Type]="IT Request")
```
``` 
Total Tickets = COUNTROWS(Tickets)
```
```
Switch measure = {
    ("SLA Compliance(%)", NAMEOF('DAX Measures'[SLA Compliance(%)]), 0),
    ("SLA Non-Compliance(%)", NAMEOF('DAX Measures'[SLA Non-Compliance(%)]), 1)
}
```



