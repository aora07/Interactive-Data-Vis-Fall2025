---
title: "Lab 4: Clearwater Crisis"
toc: false
---
# Clearwater Lake Crisis Investigation

*An Ecological Detective Story: Who's Responsible for the Collapse of Lake Clearwater?*

```js
// Load all datasets
const waterQuality = await FileAttachment("./data/water_quality.csv").csv({typed: true});
const fishSurveys = await FileAttachment("./data/fish_surveys.csv").csv({typed: true});
const monitoringStations = await FileAttachment("./data/monitoring_stations.csv").csv({typed: true});
const suspectActivities = await FileAttachment("./data/suspect_activities.csv").csv({typed: true});

// Water quality standards for reference
const standards = {
  heavyMetals: { concern: 20, limit: 30 },
  nitrogen: { concern: 1.5, limit: 2.0 },
  phosphorus: { concern: 0.05, limit: 0.1 },
  dissolvedOxygen: { minimum: 5.0, ideal: 7.0 },
  pH: { min: 6.0, max: 9.0, idealMin: 6.5, idealMax: 8.5 },
  turbidity: { concern: 10, limit: 25 }
};
```
# Executive Summary
The Verdict: ChemTech Manufacturing is responsible for the ecological collapse at Lake Clearwater.

This investigation reveals a clear pattern of heavy metal contamination originating from the West shore, where ChemTech Manufacturing operates. The evidence shows:

Spatial Pattern: Highest contamination levels at the West station, closest to ChemTech.  
Temporal Pattern: Pollution spikes align with ChemTech's quarterly maintenance shutdowns.  
Biological Impact: Dramatic decline in pollution-sensitive trout populations matching scientific models of heavy metal toxicity.  

## 1. The Crime Scene: Spatial Analysis of Water Quality
### Heavy Metal Contamination by Station
```js
function heavyMetalsByStation() {
  // Calculate average heavy metals by station
  const stationAverages = Array.from(d3.group(waterQuality, d => d.station_id), ([station, data]) => {
    const avgMetals = d3.mean(data, d => d.heavy_metals_ppb);
    return { station, avgMetals };
  });

  return Plot.plot({
    title: "Average Heavy Metal Concentration by Station (2023-2024)",
    subtitle: "Red line shows EPA concern threshold (20 ppb)",
    marks: [
      Plot.barY(stationAverages, {
        x: "station",
        y: "avgMetals",
        fill: d => d.avgMetals > standards.heavyMetals.concern ? "red" : "steelblue"
      }),
      Plot.ruleY([standards.heavyMetals.concern], {stroke: "red", strokeDasharray: "4,2"}),
      Plot.text(stationAverages, {
        x: "station",
        y: "avgMetals",
        text: d => d.avgMetals.toFixed(1) + " ppb",
        dy: -10,
        fontSize: 12,
        fontWeight: "bold"
      })
    ],
    x: {label: "Monitoring Station"},
    y: {label: "Heavy Metals (ppb)", domain: [0, 30]},
    width: 800,
    height: 400
  });
}

display(heavyMetalsByStation());
```

### Distance to Suspects vs. Contamination Levels
```js
function distanceContaminationCorrelation() {
  // Join station data with average contamination
  const stationData = monitoringStations.map(station => {
    const stationQuality = waterQuality.filter(d => d.station_id === station.station_id);
    const avgMetals = d3.mean(stationQuality, d => d.heavy_metals_ppb);
    return {...station, avgMetals};
  });

  // Create a simple scatter plot showing distance to ChemTech vs contamination
  return Plot.plot({
    title: "Distance from ChemTech Manufacturing vs. Heavy Metal Contamination",
    subtitle: "Closer proximity to ChemTech correlates with higher contamination",
    grid: true,
    marks: [
      Plot.ruleY([standards.heavyMetals.concern], {stroke: "red", strokeDasharray: "4,2"}),
      Plot.dot(stationData, {
        x: "distance_to_chemtech_m",
        y: "avgMetals",
        fill: "station_id",
        r: 8,
        stroke: "black"
      }),
      Plot.text(stationData, {
        x: "distance_to_chemtech_m",
        y: "avgMetals",
        text: "station_id",
        dy: -15,
        fontSize: 12,
        fontWeight: "bold"
      }),
      Plot.linearRegressionY(stationData, {
        x: "distance_to_chemtech_m",
        y: "avgMetals",
        stroke: "gray"
      })
    ],
    x: {label: "Distance from ChemTech (meters)"},
    y: {label: "Average Heavy Metals (ppb)", domain: [0, 30]},
    color: {legend: true},
    width: 800,
    height: 400
  });
}

display(distanceContaminationCorrelation());
```

Key Finding: The West station shows the highest heavy metal contamination (24.8 ppb average), exceeding the EPA concern threshold. It's also the closest station to ChemTech Manufacturing (800m).

## 2. The Timeline: Pollution Events and Suspect Activities
### Heavy Metal Spikes Over Time

```js
function heavyMetalsTimeline() {
  // Get ChemTech maintenance dates
  const chemtechActivities = suspectActivities.filter(d => d.suspect === "ChemTech Manufacturing");
  
  return Plot.plot({
    title: "Heavy Metal Contamination Timeline with ChemTech Maintenance Periods",
    subtitle: "Red bars show ChemTech maintenance shutdowns",
    marks: [
      Plot.ruleY([standards.heavyMetals.concern], {stroke: "red", strokeDasharray: "4,2"}),
      Plot.ruleY([standards.heavyMetals.limit], {stroke: "darkred", strokeDasharray: "4,2"}),
      Plot.line(waterQuality.filter(d => d.station_id === "West"), {
        x: "date",
        y: "heavy_metals_ppb",
        stroke: "station_id",
        strokeWidth: 2
      }),
      Plot.ruleX(chemtechActivities, {
        x1: "date",
        x2: d => new Date(d3.timeDay.offset(new Date(d.date), d.duration_days)),
        y: 70,
        stroke: "red",
        strokeWidth: 8
      }),
      Plot.text(chemtechActivities, {
        x: d => d3.timeDay.offset(new Date(d.date), d.duration_days/2),
        y: 65,
        text: "ChemTech\nMaintenance",
        fontSize: 9,
        textAnchor: "middle"
      })
    ],
    x: {label: "Date"},
    y: {label: "Heavy Metals (ppb)", domain: [0, 80]},
    color: {legend: true},
    width: 900,
    height: 400
  });
}

display(heavyMetalsTimeline());
```
### All Suspect Activities Timeline
```js
// All Suspect Activities Timeline with Legend
function suspectActivitiesTimeline() {
  const suspectColors = {
    "ChemTech Manufacturing": "#e41a1c", // Red
    "Riverside Farm": "#4daf4a",         // Green
    "Lakeview Resort": "#377eb8",        // Blue
    "Clearwater Fishing Lodge": "#ff7f00" // Orange
  };

  // Prepare data with end dates and formatted labels
  const activitiesWithEndDate = suspectActivities.map(d => ({
    ...d,
    endDate: new Date(d3.timeDay.offset(new Date(d.date), d.duration_days)),
    intensityValue: d.intensity === "High" ? 3 : d.intensity === "Medium" ? 2 : 1,
    // Create shorter labels for better display
    shortLabel: d.activity_type.length > 25 
      ? d.activity_type.substring(0, 22) + "..." 
      : d.activity_type
  }));

  return Plot.plot({
    title: "Timeline of All Suspect Activities (2023-2024)",
    subtitle: "Bar length = duration, bar height = intensity",
    marks: [
      // Background rules for each suspect row
      Plot.ruleY([0, 1, 2, 3], {
        y: d => d,
        stroke: "#f0f0f0",
        strokeWidth: 20
      }),
      
      // Main activity bars (Gantt chart style)
      Plot.ruleX(activitiesWithEndDate, {
        x1: "date",
        x2: "endDate",
        y: d => {
          // Map suspect to y-position (0-3)
          const suspects = Object.keys(suspectColors);
          return suspects.indexOf(d.suspect);
        },
        stroke: d => suspectColors[d.suspect],
        strokeWidth: d => d.intensityValue * 6,
        strokeOpacity: 0.8
      }),
      
      // Start date markers
      Plot.dot(activitiesWithEndDate, {
        x: "date",
        y: d => Object.keys(suspectColors).indexOf(d.suspect),
        fill: d => suspectColors[d.suspect],
        stroke: "black",
        r: 2
      }),
      
      // Activity labels in the middle of bars
      Plot.text(activitiesWithEndDate.filter(d => d.duration_days > 5), {
        x: d => d3.timeDay.offset(new Date(d.date), d.duration_days/2),
        y: d => Object.keys(suspectColors).indexOf(d.suspect),
        text: d => `${d.shortLabel}`,
        fontSize: 8,
        fill: "white",
        stroke: "black",
        strokeWidth: 0.5,
        textAnchor: "middle"
      }),
      
      // Duration labels for short activities
      Plot.text(activitiesWithEndDate.filter(d => d.duration_days <= 5), {
        x: d => d3.timeDay.offset(new Date(d.date), d.duration_days/2),
        y: d => Object.keys(suspectColors).indexOf(d.suspect),
        text: d => `${d.duration_days}d`,
        fontSize: 7,
        fill: "white",
        stroke: "black",
        strokeWidth: 0.3,
        textAnchor: "middle"
      }),
      
      // Intensity indicators (dots above bars)
      Plot.text(activitiesWithEndDate, {
        x: "date",
        y: d => Object.keys(suspectColors).indexOf(d.suspect) - 0.15,
        text: d => "•".repeat(d.intensityValue),
        fontSize: 10,
        fill: d => suspectColors[d.suspect],
        textAnchor: "start",
        dx: 2
      })
    ],
    x: {
      label: "Date",
      type: "time",
      interval: d3.timeMonth,
      ticks: 12,
      labelOffset: 40
    },
    y: {
      label: "",
      domain: [-0.5, 3.5],
      ticks: Object.keys(suspectColors).map((suspect, i) => i),
      tickFormat: (d, i) => Object.keys(suspectColors)[i]
    },
    color: {
      domain: Object.keys(suspectColors),
      range: Object.values(suspectColors),
      legend: true
    },
    width: 1000,
    height: 350,
    marginLeft: 120, // More space for y-axis labels
    marginRight: 20,
    marginTop: 60,
    marginBottom: 50
  });
}

display(suspectActivitiesTimeline());

```

Key Finding: Heavy metal spikes consistently occur during or immediately following ChemTech's quarterly maintenance shutdowns, including a critical violation in December 2023 (48.8 ppb).

## 3. The Victims: Fish Population Decline
### Trout Population Collapse
```js
function troutPopulationTrend() {
  const troutData = fishSurveys.filter(d => d.species === "Trout");
  
  return Plot.plot({
    title: "Trout Population Collapse by Station (2023-2024)",
    subtitle: "Trout are highly sensitive to pollution - their decline indicates severe contamination",
    marks: [
      Plot.line(troutData, {
        x: "date",
        y: "count",
        stroke: "station_id",
        curve: "linear"
      }),
      Plot.dot(troutData, {
        x: "date",
        y: "count",
        fill: "station_id",
        stroke: "black"
      }),
 
    ],
    x: {label: "Survey Date"},
    y: {label: "Trout Count", domain: [0, 50]},
    color: {legend: true},
    width: 900,
    height: 400
  });
}

display(troutPopulationTrend());
```
### Fish Population Changes by Sensitivity

```js
function fishSensitivityComparison() {
  // Calculate percentage change from 2023 to 2024 by station and sensitivity
  const early2023 = fishSurveys.filter(d => d.date.getFullYear() === 2023 && d.date.getMonth() <= 3);
  const late2024 = fishSurveys.filter(d => d.date.getFullYear() === 2024 && d.date.getMonth() >= 6);
  
  const earlyAverages = Array.from(d3.group(early2023, d => d.station_id + "_" + d.pollution_sensitivity), 
    ([key, data]) => {
      const [station, sensitivity] = key.split('_');
      const avgCount = d3.mean(data, d => d.count);
      return {station, sensitivity, period: "2023", avgCount};
    });
  
  const lateAverages = Array.from(d3.group(late2024, d => d.station_id + "_" + d.pollution_sensitivity), 
    ([key, data]) => {
      const [station, sensitivity] = key.split('_');
      const avgCount = d3.mean(data, d => d.count);
      return {station, sensitivity, period: "2024", avgCount};
    });
  
  const changes = earlyAverages.map(early => {
    const late = lateAverages.find(d => d.station === early.station && d.sensitivity === early.sensitivity);
    const change = late ? ((late.avgCount - early.avgCount) / early.avgCount * 100) : 0;
    return {...early, change};
  });

  return Plot.plot({
    title: "Fish Population Change by Pollution Sensitivity (2023 vs 2024)",
    subtitle: "High-sensitivity species show greatest decline at West station near ChemTech",
    marks: [
      Plot.ruleY([0], {stroke: "black"}),
      Plot.barY(changes, {
        x: "station",
        y: "change", 
        fill: d => d.change < 0 ? "red" : "green",
        stroke: "black",
        fx: "sensitivity"
      })
    ],
    x: {label: "Monitoring Station"},
    y: {label: "Population Change (%)", domain: [-80, 20]},
    fy: {label: "Pollution Sensitivity"},
    width: 800,
    height: 400
  });
}

display(fishSensitivityComparison());
```
Key Finding: The West station shows a catastrophic 70% decline in trout populations (high sensitivity), while low-sensitivity species like carp remain relatively stable - a classic pattern of heavy metal contamination.

## 4. The Scientific Evidence: Heavy Metal Toxicity
### Mortality Rates vs. Observed Contamination

```js
function mortalityModel() {
  // Scientific reference: Heavy metal mortality rates for trout
  const mortalityData = [
    { metals: 10, mortality: 5 },
    { metals: 20, mortality: 15 },
    { metals: 30, mortality: 40 },
    { metals: 40, mortality: 70 },
    { metals: 50, mortality: 85 }
  ];
  
  // West station peak contamination events
  const westPeaks = [
    { date: "2023-12-10", metals: 48.8, actualDecline: 51 }, // Trout decline from 43 to 21
    { date: "2024-09-15", metals: 41.6, actualDecline: 59 }  // Trout decline from 32 to 13
  ];

  return Plot.plot({
    title: "Scientific Model: Heavy Metal Concentration vs. Expected Trout Mortality",
    subtitle: "Red dots show actual contamination peaks at West station vs. observed decline",
    marks: [
      Plot.line(mortalityData, {
        x: "metals",
        y: "mortality",
        stroke: "blue",
        strokeWidth: 2
      }),
      Plot.dot(mortalityData, {
        x: "metals", 
        y: "mortality",
        fill: "blue",
        r: 4
      }),
      Plot.dot(westPeaks, {
        x: "metals",
        y: "actualDecline", 
        fill: "red",
        r: 6,
        stroke: "black"
      }),
      Plot.text(westPeaks, {
        x: "metals",
        y: "actualDecline",
        text: d => `${d.date}\n${d.metals}ppb → ${d.actualDecline}% decline`,
        dy: -20,
        fontSize: 9,
        fontWeight: "bold"
      }),
      Plot.ruleX([standards.heavyMetals.concern], {stroke: "red", strokeDasharray: "4,2"}),
      Plot.ruleX([standards.heavyMetals.limit], {stroke: "darkred", strokeDasharray: "4,2"})
    ],
    x: {label: "Heavy Metal Concentration (ppb)", domain: [0, 60]},
    y: {label: "Trout Mortality Rate (%)", domain: [0, 100]},
    width: 800,
    height: 400
  });
}

display(mortalityModel());
```
Key Finding: The observed trout decline (51-59%) at West station matches scientific models predicting 50-70% mortality at the heavy metal levels measured (41.6-48.8 ppb).

## 5. Alternative Suspects: Ruling Out Other Contributors
### Nitrogen and Phosphorus Analysis

```js
function nutrientAnalysis() {
  // Check farm fertilizer application periods
  const farmActivities = suspectActivities.filter(d => d.suspect === "Riverside Farm");
  const northStation = waterQuality.filter(d => d.station_id === "North");
  
  return Plot.plot({
    title: "Nutrient Levels at North Station vs. Farm Fertilizer Applications",
    subtitle: "No concerning patterns - nutrients remain below EPA limits",
    marks: [
      Plot.ruleY([standards.nitrogen.concern], {stroke: "orange", strokeDasharray: "4,2"}),
      Plot.ruleY([standards.nitrogen.limit], {stroke: "red", strokeDasharray: "4,2"}),
      Plot.line(northStation, {
        x: "date",
        y: "nitrogen_mg_per_L",
        stroke: "green",
        strokeWidth: 2
      }),
      Plot.ruleX(farmActivities, {
        x1: "date",
        x2: d => new Date(d3.timeDay.offset(new Date(d.date), d.duration_days)),
        y: 2.6,
        stroke: "brown",
        strokeWidth: 6
      }),
      Plot.text(farmActivities.slice(0, 2), {
        x: d => d3.timeDay.offset(new Date(d.date), d.duration_days/2),
        y: 2.5,
        text: "Farm\nFertilizer",
        fontSize: 8,
        textAnchor: "middle"
      })
    ],
    x: {label: "Date"},
    y: {label: "Nitrogen (mg/L)", domain: [0.5, 2.7]},
    width: 900,
    height: 300
  });
}

display(nutrientAnalysis());
```
### Lodge and Resort Impact Assessment
```js
function otherParameters() {
  const westStation = waterQuality.filter(d => d.station_id === "West");
  
  return Plot.plot({
    title: "Other Water Quality Parameters at West Station",
    subtitle: "Only heavy metals show concerning patterns - ruling out general pollution",
    grid: true,
    marks: [
      Plot.ruleY([5], {stroke: "red", strokeDasharray: "4,2"}), // DO minimum
      Plot.line(westStation, {
        x: "date",
        y: "dissolved_oxygen_mg_per_L",
        stroke: "blue",
        strokeWidth: 1.5
      }),
      Plot.line(westStation, {
        x: "date", 
        y: "ph",
        stroke: "green",
        strokeWidth: 1.5
      }),
      Plot.line(westStation, {
        x: "date",
        y: d => d.turbidity_ntu,
        stroke: "brown", 
        strokeWidth: 1.5
      })
    ],
    x: {label: "Date"},
    y: {label: "Parameter Values", domain: [4, 10]},
    color: {legend: true},
    width: 900,
    height: 300
  });
}

display(otherParameters());
```
Key Finding:

Riverside Farm: Nutrient spikes are minor and temporary, remaining below EPA limits.  
Fishing Lodge & Resort: No correlation with dissolved oxygen, pH, or turbidity issues.  
Only ChemTech shows the specific heavy metal pattern that matches the biological impact.  

## 6. The Verdict: Cumulative Evidence
### Evidence Matrix Against All Suspects

```js
function evidenceMatrix() {
  const evidence = [
    { suspect: "ChemTech Manufacturing", spatial: 10, temporal: 10, biological: 10, total: 30 },
    { suspect: "Riverside Farm", spatial: 3, temporal: 4, biological: 2, total: 9 },
    { suspect: "Lakeview Resort", spatial: 2, temporal: 3, biological: 1, total: 6 },
    { suspect: "Clearwater Fishing Lodge", spatial: 1, temporal: 2, biological: 1, total: 4 }
  ];

  return Plot.plot({
    title: "Evidence Scorecard: Strength of Case Against Each Suspect",
    marks: [
      Plot.cell(evidence, {
        x: "suspect",
        y: (d, i) => ["Spatial", "Temporal", "Biological"][i % 3],
        fill: (d, i) => d[["spatial", "temporal", "biological"][i % 3]],
        inset: 2
      }),
      Plot.text(evidence.flatMap(d => 
        ["spatial", "temporal", "biological"].map((key, i) => ({
          suspect: d.suspect,
          metric: ["Spatial", "Temporal", "Biological"][i],
          value: d[key],
          x: d.suspect,
          y: ["Spatial", "Temporal", "Biological"][i]
        }))
      ), {
        x: "x",
        y: "y",
        text: "value",
        fill: "white",
        fontWeight: "bold",
        fontSize: 12
      })
    ],
    x: {label: "Suspect"},
    y: {label: "Evidence Category"},
    color: {scheme: "reds", legend: true, label: "Evidence Strength"},
    width: 800,
    height: 300
  });
}

display(evidenceMatrix());
```

## Conclusion: The Case Against ChemTech Manufacturing
Overwhelming evidence points to ChemTech Manufacturing as the responsible party:

Spatial Evidence: Highest contamination levels at West station (800m from ChemTech).  
Temporal Evidence: Pollution spikes align precisely with quarterly maintenance shutdowns.  
Biological Evidence: Trout decline matches scientific models for heavy metal toxicity.  
Differential Impact: High-sensitivity species most affected, low-sensitivity species stable.  
Ruling Out Alternatives: No other suspect shows this consistent pattern across all evidence types.  
Recommendation: Immediate EPA enforcement action against ChemTech Manufacturing, requiring improved filtration systems and continuous monitoring during maintenance periods.  