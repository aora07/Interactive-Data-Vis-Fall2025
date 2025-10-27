<!DOCTYPE html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Pollinator Dashboard | Interactive Data Visualization</title>

  <!-- Observable Framework Styles -->
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link rel="preload" as="style" href="https://fonts.googleapis.com/css2?family=Source+Serif+4:wght@400;600&display=swap">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@observablehq/plot@0.6.14/dist/plot.css">
  <script src="https://cdn.jsdelivr.net/npm/@observablehq/plot@0.6.14/dist/plot.umd.min.js"></script>

  <style>
    body {
      font-family: "Source Serif 4", serif;
      margin: 2rem auto;
      max-width: 900px;
      line-height: 1.6;
      color: #333;
    }
    h1, h2 {
      color: #222;
    }
    .plot {
      margin: 2rem 0;
    }
    header {
      border-bottom: 2px solid #ddd;
      margin-bottom: 2rem;
      padding-bottom: 1rem;
    }
    .credit {
      font-size: 0.9em;
      color: #777;
    }
  </style>
</head>
<body>

<header>
  <h1>Lab 1: Prolific Pollinators</h1>
  <p class="credit">Interactive Data Visualization (Fall 2025)</p>
</header>

<section>
  <h2>1. Body Mass and Wingspan Distribution</h2>
  <p>This chart shows the distribution of body mass and wingspan for each pollinator species observed on the farm. It helps us understand which species are largest or smallest, and how body size relates to pollination capacity.</p>
  <div id="plot-mass" class="plot"></div>
</section>

<section>
  <h2>2. Ideal Weather Conditions for Pollination</h2>
  <p>Bees are more active under certain weather conditions. This visualization explores temperature, humidity, and other environmental factors that correlate with increased pollination activity.</p>
  <div id="plot-weather" class="plot"></div>
</section>

<section>
  <h2>3. Nectar Production by Flower Type</h2>
  <p>Which flower species yield the most nectar? This bar chart summarizes nectar production across different flowers observed in the dataset.</p>
  <div id="plot-nectar" class="plot"></div>
</section>

<script type="module">
  import * as Plot from "https://cdn.jsdelivr.net/npm/@observablehq/plot@0.6.14/dist/plot.umd.min.js";

  // Load CSV
  const dataUrl = "./data/pollinator_activity_data.csv"; // adjust path if needed
  const data = await d3.csv(dataUrl, d3.autoType);

  // 1. Body mass vs wingspan by species
  const massPlot = Plot.plot({
    title: "Body Mass vs. Wingspan by Species",
    x: { label: "Body Mass (mg)" },
    y: { label: "Wing Span (mm)" },
    color: { legend: true, label: "Species" },
    marks: [
      Plot.dot(data, { x: "body_mass", y: "wing_span", fill: "species", title: "species" })
    ]
  });
  document.getElementById("plot-mass").append(massPlot);

  // 2. Weather conditions vs activity
  const weatherPlot = Plot.plot({
    title: "Pollinator Activity vs. Temperature",
    x: { label: "Temperature (°C)" },
    y: { label: "Pollination Count" },
    color: { legend: true, label: "Condition" },
    marks: [
      Plot.dot(data, { x: "temperature", y: "activity_count", fill: "weather_condition" }),
      Plot.lineY(data, Plot.groupX({ y: "mean" }, { x: "temperature", y: "activity_count" }))
    ]
  });
  document.getElementById("plot-weather").append(weatherPlot);

  // 3. Nectar production by flower
  const nectarPlot = Plot.plot({
    title: "Average Nectar Production per Flower Species",
    x: { label: "Flower Species" },
    y: { label: "Average Nectar (μL)" },
    marks: [
      Plot.barY(data, Plot.groupX({ y: "mean" }, { x: "flower_species", y: "nectar_volume", fill: "flower_species" }))
    ]
  });
  document.getElementById("plot-nectar").append(nectarPlot);
</script>

<!-- D3 for data loading -->
<script src="https://cdn.jsdelivr.net/npm/d3@7/dist/d3.min.js"></script>

</body>
</html>
