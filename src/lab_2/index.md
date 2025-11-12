---
title: "Lab 2: NYC Subway Staffing Dashboard"
toc: true
---

# Lab 2: NYC Subway Staffing — 2025–2026 Analysis

This dashboard explores how **events, ridership, and incident response** affect staffing needs across Manhattan subway stations.

---

```js
import * as Plot from "npm:@observablehq/plot";

// ✅ Load data (must be in /data)
const ridership = await FileAttachment("./data/ridership.csv").csv({ typed: true });
const localEvents = await FileAttachment("./data/local_events.csv").csv({ typed: true });
const incidents = await FileAttachment("./data/incidents.csv").csv({ typed: true });
const upcoming = await FileAttachment("./data/upcoming_events.csv").csv({ typed: true });

const sum = (arr) => arr.reduce((a, b) => a + b, 0);
function groupBy(arr, keyFn) {
  const m = new Map();
  for (const d of arr) {
    const k = keyFn(d);
    if (!m.has(k)) m.set(k, []);
    m.get(k).push(d);
  }
  return m;
}

const dailyRidership = Array.from(
  groupBy(ridership, d => d.date),
  ([date, rows]) => ({
    date: new Date(date),
    total: sum(rows.map(r => r.entrances + r.exits))
  })
).sort((a, b) => a.date - b.date);

const fareChange = new Date("2025-07-15");
const pre = dailyRidership.filter(d => d.date < fareChange);
const post = dailyRidership.filter(d => d.date >= fareChange);
const avgPre = sum(pre.map(d => d.total)) / pre.length;
const avgPost = sum(post.map(d => d.total)) / post.length;
const pctChange = ((avgPost - avgPre) / avgPre) * 100;
```
```js
Plot.plot({
  title: "Daily Ridership (Entrances + Exits), Summer 2025",
  marginLeft: 60,
  marginRight: 30,
  width: 900,
  height: 400,
  x: { label: "Date" },
  y: { label: "Total Riders", grid: true },
  marks: [
    // Smooth line for ridership totals
    Plot.lineY(dailyRidership, {
      x: "date",
      y: "total",
      stroke: "#2b6cb0",
      strokeWidth: 2
    }),

    // Red vertical line for fare increase
    Plot.ruleX([fareChange], {
      stroke: "red",
      strokeWidth: 2
    }),

    // Annotation for the fare increase
    Plot.text(
      [
        {
          date: fareChange,
          total: Math.max(...dailyRidership.map(d => d.total)) * 0.95,
          label: "Fare increase → $3.00"
        },
        {
          date: fareChange,
          total: Math.max(...dailyRidership.map(d => d.total)) * 0.90,
          label: "July 15, 2025"
        }
      ],
      {
        x: "date",
        y: "total",
        text: "label",
        fill: "red",
        fontWeight: "bold",
        textAnchor: "start",
        dx: 10,
        dy: -5
      }
    )
  ]
})
```

Summary:
Before the fare change, average daily ridership ≈ ${Math.round(avgPre).toLocaleString()},
after ≈ ${Math.round(avgPost).toLocaleString()} (${pctChange.toFixed(1)}% change).
Event-heavy days continue to spike despite the fare hike.

```js
const avgResponse = Array.from(
  groupBy(incidents, d => d.station),
  ([station, rows]) => ({
    station,
    avg: sum(rows.map(r => r.response_time_minutes)) / rows.length
  })
).sort((a, b) => a.avg - b.avg);

const meanResponse =
  sum(avgResponse.map(d => d.avg)) / avgResponse.length;
```
```js
  Plot.plot({
  title: "Average Incident Response Time per Station",
  marginLeft: 200,
  x: { label: "Response Time (minutes)" },
  y: { label: null, domain: avgResponse.map(d => d.station) },
  marks: [
    Plot.barX(avgResponse, { x: "avg", y: "station", tip: true }),
    Plot.ruleX([meanResponse], { stroke: "red", strokeDasharray: "4 2" }),
    Plot.text(
      [meanResponse],
      {
        x: "x",
        y: avgResponse[0].station,
        text: () => `Mean = ${meanResponse.toFixed(1)} min`,
        fill: "red",
        anchor: "start"
      }
    )
  ]
})
```

```js
Plot.plot({
  title: "Distribution of Response Times (binned)",
  marginLeft: 60,
  x: { label: "Response Time (minutes)" },
  y: { label: "Incident Count" },
  marks: [
    Plot.rectY(incidents, Plot.binX({ y: "count" }, { x: "response_time_minutes" })),
    Plot.ruleX([meanResponse], { stroke: "red" }),
    Plot.text(
      [meanResponse],
      { x: "x", y: 5, text: () => "System Mean", fill: "red", dy: -6 }
    )
  ]
})
```

```js
const staffByStation = Array.from(
  groupBy(incidents, d => d.station),
  ([station, rows]) => ({
    station,
    staff: Math.max(...rows.map(r => r.staffing_count))
  })
);

const upcomingTotals = Array.from(
  groupBy(upcoming, d => d.nearby_station),
  ([station, rows]) => ({
    station,
    expected: sum(rows.map(r => r.expected_attendance))
  })
);

const demand = upcomingTotals.map(d => {
  const staff = staffByStation.find(s => s.station === d.station)?.staff ?? 0;
  return {
    station: d.station,
    expected: d.expected,
    staff,
    perStaff: staff > 0 ? d.expected / staff : 0
  };
}).sort((a, b) => b.perStaff - a.perStaff);

const top3 = demand.slice(0, 3);
```
```js
Plot.plot({
  title: "Projected 2026 Event Load per Staff Member",
  marginLeft: 200,
  x: { label: "Expected Attendees per Staff" },
  y: { label: null, domain: demand.map(d => d.station) },
  marks: [
    Plot.barX(demand, {
      x: "perStaff",
      y: "station",
      fill: d => (top3.some(t => t.station === d.station) ? "crimson" : "#bbb"),
      tip: true
    }),
    Plot.text(
      top3.map(d => ({ ...d, label: "Top 3" })),
      { x: "perStaff", y: "station", text: "label", fill: "crimson", dx: 10, anchor: "start" }
    )
  ]
})
```
```js
// Simple composite: 60% demand, 40% response time (higher = worse)
const responseMap = new Map(avgResponse.map(d => [d.station, d.avg]));
demand.forEach(d => {
  const r = responseMap.get(d.station) ?? meanResponse;
  d.score = 0.6 * (d.perStaff / Math.max(...demand.map(x => x.perStaff))) +
            0.4 * (r / meanResponse);
});
demand.sort((a, b) => b.score - a.score);
const priority = demand[0];
```

```js
Plot.plot({
  title: "Composite Staffing Priority (Top 10 Stations)",
  marginLeft: 200,
  x: { label: "Priority Score (0–1 scale)" },
  y: { label: null, domain: demand.slice(0, 10).map(d => d.station) },
  marks: [
    Plot.barX(demand.slice(0, 10), { x: "score", y: "station", tip: true }),
    Plot.text([{ x: priority.score, y: priority.station, label: "★ Priority" }],
      { x: "x", y: "y", text: "label", fill: "gold", dx: 15 })
  ]
})
```
Top priority station: ${priority.station}, based on both high projected load and slower response performance.