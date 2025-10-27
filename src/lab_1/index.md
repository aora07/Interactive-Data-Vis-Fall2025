<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Pollinator Dashboard | Interactive Data Visualization</title>

  <!-- Fonts & Styles -->
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Source+Serif+4:wght@400;600&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@observablehq/plot@0.6.14/dist/plot.css">

  <style>
    body {
      font-family: "Source Serif 4", serif;
      margin: 2rem auto;
      max-width: 900px;
      line-height: 1.6;
      color: #333;
    }
    h1, h2 { color: #222; }
    header { border-bottom: 2px solid #ddd; margin-bottom: 2rem; padding-bottom: 1rem; }
    .credit { font-size: 0.9em; color: #777; }
    .plot { margin: 2rem 0; }
  </style>
</head>

<body>
<header>
  <h1>Lab 1: Prolific Pollinators</h1>
  <p class="credit">Interactive Data Visualization (Fall 2025)</p>
</header>

<section>
  <h2>1. Body Mass and Wingspan Distribution</h2>
  <p>This chart compares average body mass and wingspan across pollinator species observed on the farm.</p>
  <div id="plot-mass" class="plot"></div>
</section>

<section>
  <h2>2. Ideal Weather Conditions for Pollination</h2>
  <p>Which weather conditions result in higher pollination activity? This chart shows temperature versus visit count, colored by weather condition.</p>
  <div id="plot-weather" class="plot"></div>
</section>

<section>
  <h2>3. Nectar Production by Flower Species</h2>
  <p>Which flowers produce the most nectar? This bar chart compares average nectar production for each flower species.</p>
  <div id="plot-nectar" class="plot"></div>
</section>

<!-- Load libraries -->
<script src="https://cdn.jsdelivr.net/npm/d3@7/dist/d3.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@observablehq/plot@0.6.14/dist/plot.umd.min.js"></script>

<script>
(async () => {
  // Load dataset
  const data = await d3.csv("./data/pollinator_activity_data.csv", d3.autoType);

  // 1️⃣ Body Mass vs Wingspan
  const massPlot = Plot.plot({
    title: "Average Body Mass vs Wingspan by Pollinator Species",
    x: { label: "Average Body Mass (g)" },
    y: { label: "Average Wingspan (mm)" },
    color: { label: "Pollinator Species", legend: true },
    marks: [
      Plot.dot(data, { 
        x: "avg_body_mass_g", 
        y: "avg_wing_span_mm", 
        fill: "pollinator_species", 
        title: d => `${d.pollinator_species}`
      })
    ]
  });
  document.getElementById("plot-mass").appendChild(massPlot);

  // 2️⃣ Weather vs Pollination Activity
  const weatherPlot = Plot.plot({
    title: "Pollination Activity vs Temperature",
    x: { label: "Temperature (°C)" },
    y: { label: "Visit Count" },
    color: { label: "Weather Condition", legend: true },
    marks: [
      Plot.dot(data, { 
        x: "temperature", 
        y: "visit_count", 
        fill: "weather_condition", 
        title: d => `${d.weather_condition}` 
      }),
      Plot.lineY(data, 
        Plot.groupX({ y: "mean" }, { x: "temperature", y: "visit_count", stroke: "steelblue" })
      )
    ]
  });
  document.getElementById("plot-weather").appendChild(weatherPlot);

  // 3️⃣ Nectar Production per Flower
  const nectarPlot = Plot.plot({
    title: "Average Nectar Production per Flower Species",
    x: { label: "Flower Species", tickRotate: -45 },
    y: { label: "Average Nectar Production" },
    marks: [
      Plot.barY(data, 
        Plot.groupX({ y: "mean" }, { 
          x: "flower_species", 
          y: "nectar_production", 
          fill: "flower_species" 
        })
      )
    ]
  });
  document.getElementById("plot-nectar").appendChild(nectarPlot);
})();
</script>
</body>
</html>
