---
title: Pollinator Activity Dashboard
---

# 🐝 Pollinator Activity Dashboard

import * as Plot from "@observablehq/plot";

// ✅ Load dataset using Observable's FileAttachment system
const data = await FileAttachment("../data/pollinator_activity_data.csv").csv({
  typed: true
});

---

## 1️⃣ Body Mass & Wing Span by Pollinator Species

<div class="chart">
{Plot.plot({
  grid: true,
  marks: [
    Plot.dot(data, {
      x: "avg_body_mass_g",
      y: "avg_wing_span_mm",
      fill: "pollinator_species",
      title: d => `${d.pollinator_species}
Mass: ${d.avg_body_mass_g}g
Wingspan: ${d.avg_wing_span_mm}mm`
    })
  ],
  x: {label: "Body Mass (g)"},
  y: {label: "Wing Span (mm)"},
  color: {legend: true}
})}
</div>

---

## 2️⃣ Ideal Weather for Pollinating

<div class="chart">
{Plot.plot({
  grid: true,
  marks: [
    Plot.dot(data, {
      x: "temperature",
      y: "visit_count",
      stroke: "weather_condition",
      fill: "weather_condition",
      title: d => `${d.weather_condition}
Visits: ${d.visit_count}
Temp: ${d.temperature}°C`
    })
  ],
  x:

