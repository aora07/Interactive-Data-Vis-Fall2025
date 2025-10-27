---
title: "Lab 1: Passing Pollinators"
toc: true
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](/README.md).
[];

🐝 Pollinator Activity Dashboard
Data Context

This dashboard analyzes June 2024 field observations from a local farm to understand bee behavior, flower nectar yield, and weather impacts on pollination.

1️⃣ Body Mass and Wing Span Distribution
<div id="mass_wingspan"></div>

Insight:
Honeybees are smallest (≈0.10 g, 19 mm wingspan), Bumblebees are mid-sized (≈0.25 g, 35 mm), and Carpenter Bees are largest (≈0.45 g, 42 mm).

2️⃣ Ideal Weather Conditions for Pollination
<div id="weather_trends"></div>

Insight:
Pollinators are most active around 28 °C, under Cloudy conditions, and with 60–70% humidity.

3️⃣ Nectar Production by Flower Species
<div id="nectar_chart"></div>

Insight:
🌻 Sunflowers have the highest nectar yield (≈0.94 μL), followed by Coneflowers (≈0.64 μL) and Lavender (≈0.54 μL).

📊 Data Source

Pollinator Activity Field Study Dataset (June 2024) — Observations across Plots A–D.

<script src="https://cdn.plot.ly/plotly-latest.min.js"></script> <script> // ---------- Body Mass & Wing Span ---------- var trace1 = { x: ['Honeybee','Bumblebee','Carpenter Bee'], y: [0.10, 0.25, 0.45], name: 'Avg Body Mass (g)', type: 'bar' }; var trace2 = { x: ['Honeybee','Bumblebee','Carpenter Bee'], y: [19.0, 34.8, 42.3], name: 'Avg Wing Span (mm)', type: 'bar', yaxis: 'y2' }; Plotly.newPlot('mass_wingspan', [trace1, trace2], { title: 'Body Mass & Wing Span by Pollinator Species', yaxis: { title: 'Body Mass (g)' }, yaxis2: { title: 'Wing Span (mm)', overlaying: 'y', side: 'right' }, barmode: 'group' }); // ---------- Weather Trends ---------- var trace3 = { x: [16,18,20,22,24,26,28,30], y: [3.27,3.00,4.01,5.34,5.92,6.49,12.04,0], name: 'Visits vs Temperature', mode: 'lines+markers' }; var trace4 = { x: ['Cloudy','Partly Cloudy','Sunny'], y: [6.17,5.30,4.60], name: 'Visits by Weather Condition', type: 'bar', marker: {opacity: 0.6} }; var trace5 = { x: ['60–70%','70–80%','80–90%'], y: [5.86,5.03,5.31], name: 'Visits by Humidity', type: 'bar' }; Plotly.newPlot('weather_trends', [trace3, trace4, trace5], { title: 'Pollinator Activity vs Weather Conditions', xaxis: { title: 'Temperature (°C) / Condition / Humidity' }, yaxis: { title: 'Avg Visit Count' } }); // ---------- Nectar Production ---------- var trace6 = { x: ['Sunflower','Coneflower','Lavender'], y: [0.94,0.64,0.54], type: 'bar', marker: { color: ['#f6c700','#e58aff','#c1a3ff'] } }; Plotly.newPlot('nectar_chart', [trace6], { title: 'Average Nectar Production per Flower Species', yaxis: { title: 'Nectar Production (μL)' } }); </script>