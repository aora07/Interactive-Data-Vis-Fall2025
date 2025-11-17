---
title: "Lab 2: NYC Subway Staffing Dashboard"
toc: true
---

# NYC Subway Operations Dashboard: Summer 2025-2026 Analysis

This dashboard analyzes NYC subway operations data to help the Transit Authority make informed staffing decisions for summer 2026.

## 1. Impact of Local Events and Fare Increase on Summer 2025 Ridership

To understand how local events affected ridership patterns, we can examine the relationship between event attendance and subway usage. The fare increase on July 15th provides another important variable to analyze.

```js
// Load the data
const ridership = await FileAttachment("./data/ridership.csv").csv({typed: true});
const localEvents = await FileAttachment("./data/local_events.csv").csv({typed: true});
```
```js
// Calculate daily total ridership using proper Plot.group syntax
Plot.plot({
  title: "Daily Ridership Trends with Fare Increase (Summer 2025)",
  subtitle: "Red line marks July 15th fare increase from $2.75 to $3.00",
  marks: [
    Plot.lineY(ridership, Plot.groupX({y: "sum"}, {
      x: "date",
      y: "entrances",
      stroke: "steelblue"
    })),
    Plot.ruleX([new Date("2025-07-15")], {stroke: "red", strokeWidth: 2}),
    Plot.text([{x: new Date("2025-07-15"), y: 300000, text: "Fare Increase", frameAnchor: "left"}], {
      x: "x", y: "y", text: "text"
    })
  ],
  x: {label: "Date"},
  y: {label: "Total Daily Entrances"},
  width: 800
})
```
```js
// Create event impact data by combining events with ridership
const eventImpactData = localEvents.map(event => {
  // Find ridership for this event's date and station
  const dayRidership = ridership.find(r => 
    r.date.getTime() === event.date.getTime() && 
    r.station === event.nearby_station
  );
  
  return {
    ...event,
    stationRidership: dayRidership ? dayRidership.entrances : 0,
    baselineRidership: getBaselineRidership(event.nearby_station)
  };
});

// Helper function to get typical ridership for a station
function getBaselineRidership(station) {
  const stationData = ridership.filter(r => r.station === station);
  return stationData.length > 0 ? 
    d3.mean(stationData, d => d.entrances) : 0;
}
```
```js
// Event impact analysis
Plot.plot({
  title: "Event Size vs. Ridership Increase at Nearby Stations",
  marks: [
    Plot.dot(eventImpactData, {
      x: "estimated_attendance", 
      y: d => d.stationRidership - d.baselineRidership,
      stroke: "nearby_station",
      tip: true
    }),
    Plot.linearRegressionY(eventImpactData, {
      x: "estimated_attendance", 
      y: d => d.stationRidership - d.baselineRidership
    })
  ],
  x: {label: "Event Attendance"},
  y: {label: "Ridership Increase vs Baseline"},
  width: 800
})
```

Analysis: The visualization shows a positive correlation between event attendance and ridership at nearby stations. Larger events (those with 10,000+ attendees) consistently resulted in ridership increases of 15-25% at the closest stations. The fare increase on July 15th appears to have had a minimal impact on overall ridership patterns, suggesting that demand for subway transportation remains relatively inelastic during the summer event season.

## 2. Station Response Time Analysis

Understanding response time performance across stations helps identify where operational improvements are needed.

```js
// Load incidents data
const incidents = await FileAttachment("./data/incidents.csv").csv({typed: true});
```
```js
// Station response time comparison using Plot.group
Plot.plot({
  title: "Average Response Time by Station (2015-2025)",
  marks: [
    Plot.barX(incidents, Plot.groupY({x: "mean"}, {
      y: "station",
      x: "response_time_minutes",
      sort: {y: "x", reverse: true},
      fill: (d) => d.response_time_minutes > 8 ? "red" : "steelblue"
    })),
    Plot.ruleX([8], {stroke: "red", strokeDasharray: "4,2"}),
    Plot.text([{x: 12, y: "Times Sq-42 St", text: "Slowest", frameAnchor: "left"}], {
      x: "x", y: "y", text: "text"
    }),
    Plot.text([{x: 4.5, y: "14 St-Union Sq", text: "Fastest", frameAnchor: "right"}], {
      x: "x", y: "y", text: "text"
    })
  ],
  x: {label: "Average Response Time (minutes)"},
  y: {label: "Station"},
  marginLeft: 100,
  width: 800,
  height: 500
})

```
```js
// Alternative visualization: Scatter plot with staffing vs response time
Plot.plot({
  title: "Response Time vs Staffing Level by Station",
  marks: [
    Plot.dot(incidents, {
      x: "staffing_count",
      y: "response_time_minutes",
      stroke: "station",
      tip: true
    }),
    Plot.linearRegressionY(incidents, {
      x: "staffing_count",
      y: "response_time_minutes",
      stroke: "red"
    }),
    Plot.text([{x: 15, y: 12, text: "Times Sq: High staffing,\nslow response", frameAnchor: "bottom-left"}], {
      x: "x", y: "y", text: "text"
    }),
    Plot.text([{x: 5, y: 5, text: "14 St-Union Sq:\nLow staffing, fast response", frameAnchor: "top-right"}], {
      x: "x", y: "y", text: "text"
    })
  ],
  x: {label: "Staffing Count"},
  y: {label: "Response Time (minutes)"},
  width: 800
})

```
Analysis: Stations show significant variation in response times. Times Square-42 St has the highest average response time at 12.3 minutes, despite having above-average staffing levels. This suggests inefficiencies in their incident management protocols. Conversely, 14 St-Union Sq achieves the best response time (4.8 minutes) with minimal staffing, indicating effective operational procedures. The red dashed line marks the 8-minute target response time - stations above this line require attention.

## 3. Staffing Recommendations for Summer 2026 Events

```js
const upcomingEvents = await FileAttachment("./data/upcoming_events.csv").csv({typed: true});

const currentStaffing = {
  "Times Sq-42 St": 19, "Grand Central-42 St": 18, "34 St-Penn Station": 15,
  "14 St-Union Sq": 4, "Fulton St": 17, "42 St-Port Authority": 14,
  "Herald Sq-34 St": 15, "Canal St": 4, "59 St-Columbus Circle": 6,
  "125 St": 7, "96 St": 19, "86 St": 19, "72 St": 10, "66 St-Lincoln Center": 15,
  "50 St": 20, "28 St": 13, "23 St": 8, "Christopher St": 15, "Houston St": 18,
  "Spring St": 12, "Chambers St": 18, "Wall St": 9, "Bowling Green": 6,
  "West 4 St-Wash Sq": 4, "Astor Pl": 7
};
```
```js
Plot.plot({
  title: "Summer 2026 Event Load by Station",
  subtitle: "Top 3 stations needing additional staff based on event load",
  marks: [
    Plot.barX(upcomingEvents, Plot.groupY({x: "sum"}, {
      y: "nearby_station",
      x: "expected_attendance",
      sort: {y: "x", reverse: true},
      fill: "steelblue"
    })),
    Plot.text([
      {x: 220000, y: "Times Sq-42 St", text: "#1 Priority", frameAnchor: "middle"},
      {x: 130000, y: "34 St-Penn Station", text: "#2 Priority", frameAnchor: "left"},
      {x: 130000, y: "Grand Central-42 St", text: "#3 Priority", frameAnchor: "left"}
    ], {x: "x", y: "y", text: "text", fontSize: 12})
  ],
  x: {label: "Total Expected Attendance"},
  y: {label: "Station"},
  marginLeft: 120,
  width: 800,
  height: 500
})
```
Analysis: Based on the 2026 event calendar, three stations require immediate staffing attention:

Times Sq-42 St: Highest event load by far with over 200,000 expected attendees.  
34 St-Penn Station: Significant event volume with 130,000+ expected attendees.  
Grand Central-42 St: Major transportation hub with heavy event schedule and 130,000+ expected attendees.  
These stations show the highest projected attendance for summer 2026 events and will need additional staff to manage the increased passenger flow.

## 4. [BONUS] Priority Station Recommendation

```js
// Create comparison data for the top 3 stations with better scaling
const topStations = ["Times Sq-42 St", "34 St-Penn Station", "Grand Central-42 St"];

// Calculate metrics for each station with more realistic values
const priorityData = topStations.map(station => {
  // Calculate event load (in thousands for better scale)
  const stationEvents = upcomingEvents.filter(event => event.nearby_station === station);
  const totalEventLoad = stationEvents.reduce((sum, event) => sum + event.expected_attendance, 0) / 1000;
  
  // Get average response time
  const stationIncidents = incidents.filter(incident => incident.station === station);
  const avgResponseTime = stationIncidents.length > 0 ? 
    stationIncidents.reduce((sum, incident) => sum + incident.response_time_minutes, 0) / stationIncidents.length : 0;
  
  return {
    station: station,
    eventLoad: totalEventLoad, // Now in thousands
    responseTime: avgResponseTime,
    currentStaff: currentStaffing[station] || 0,
    priorityScore: (totalEventLoad * avgResponseTime) / (currentStaffing[station] || 1)
  };
});
```
```js
// Create a better scaled visualization
Plot.plot({
  title: "Priority Station Analysis: Event Load vs Response Time",
  subtitle: "Circle size represents overall priority score",
  marks: [
    Plot.dot(priorityData, {
      x: "eventLoad", 
      y: "responseTime",
      stroke: "station",
      fill: "station",
      r: d => Math.sqrt(d.priorityScore) * 0.5, // Better scaling with square root
      tip: true
    }),
    Plot.text(priorityData, {
      x: "eventLoad", 
      y: "responseTime",
      text: "station",
      dy: -15,
      fontSize: 12,
      fontWeight: "bold"
    }),
    Plot.text([{x: 180, y: 11.5, text: "Highest Priority:\nEvent Load × Response Time ÷ Staff", frameAnchor: "bottom-right"}], {
      x: "x", y: "y", text: "text",
      fontSize: 10
    })
  ],
  x: {
    label: "Total Event Load (thousands of attendees)",
    domain: [0, 250] // Set appropriate domain
  },
  y: {
    label: "Average Response Time (minutes)",
    domain: [0, 15] // Set appropriate domain
  },
  color: {legend: true},
  width: 800
})

```

Analysis: Times Sq-42 St should be the top priority because it has both the highest event load (~220K attendees) and the worst response time (12.3 minutes), creating the largest priority score. The visualization uses a priority score calculated as (Event Load × Response Time ÷ Current Staff) to account for all critical factors.

Conclusion
Based on our analysis, the NYC Transit Authority should prioritize staffing increases at:

Times Sq-42 St (top priority) - Critical combination of high demand and poor performance
34 St-Penn Station - Significant event volume requiring attention
Grand Central-42 St - Major hub with heavy event schedule
These measures will help ensure safe and efficient subway operations during the busy summer 2026 event season.
