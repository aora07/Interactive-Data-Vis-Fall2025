---
title: "Lab 1: Passing Pollinators"
toc: true
---
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
<meta name="generator" content="Observable Framework v1.13.3">
<title>Lab 1: Passing Pollinators | Interactive Data Visualization (Fall 2025)</title>
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link rel="preload" as="style" href="https://fonts.googleapis.com/css2?family=Source+Serif+4:ital,opsz,wght@0,8..60,200..900;1,8..60,200..900&amp;display=swap" crossorigin>
<link rel="preload" as="style" href="../_observablehq/theme-air,near-midnight.dcdbf18e.css">
<link rel="preload" as="style" href="../_observablehq/stdlib/inputs.ea9fd553.css">
<link rel="stylesheet" type="text/css" href="https://fonts.googleapis.com/css2?family=Source+Serif+4:ital,opsz,wght@0,8..60,200..900;1,8..60,200..900&amp;display=swap" crossorigin>
<link rel="stylesheet" type="text/css" href="../_observablehq/theme-air,near-midnight.dcdbf18e.css">
<link rel="stylesheet" type="text/css" href="../_observablehq/stdlib/inputs.ea9fd553.css">
<link rel="modulepreload" href="../_observablehq/client.2ebf0c42.js">
<link rel="modulepreload" href="../_observablehq/runtime.e080113b.js">
<link rel="modulepreload" href="../_observablehq/stdlib.73a8ec5a.js">
<link rel="modulepreload" href="../_observablehq/stdlib/inputs.4ef1d259.js">
<link rel="modulepreload" href="../_npm/@observablehq/plot@0.6.17/d761ef9b.js">
<link rel="modulepreload" href="../_npm/d3-dsv@3.0.1/9cffc2bd.js">
<link rel="modulepreload" href="../_npm/htl@0.3.1/72f4716c.js">
<link rel="modulepreload" href="../_npm/isoformat@0.2.1/18cbf477.js">
<link rel="modulepreload" href="../_npm/d3@7.9.0/e780feca.js">
<link rel="modulepreload" href="../_npm/interval-tree-1d@1.0.4/53fe8176.js">
<link rel="modulepreload" href="../_npm/d3-array@3.2.4/e93ca09f.js">
<link rel="modulepreload" href="../_npm/d3-axis@3.0.0/0f2de24d.js">
<link rel="modulepreload" href="../_npm/d3-brush@3.0.0/65eb105b.js">
<link rel="modulepreload" href="../_npm/d3-chord@3.0.1/7ef8fb2e.js">
<link rel="modulepreload" href="../_npm/d3-color@3.1.0/aeb57b94.js">
<link rel="modulepreload" href="../_npm/d3-contour@4.0.2/1d2aed74.js">
<link rel="modulepreload" href="../_npm/d3-delaunay@6.0.4/5ced1d52.js">
<link rel="modulepreload" href="../_npm/d3-dispatch@3.0.1/9ba9c7f3.js">
<link rel="modulepreload" href="../_npm/d3-drag@3.0.0/4202580c.js">
<link rel="modulepreload" href="../_npm/d3-ease@3.0.1/cdd7e898.js">
<link rel="modulepreload" href="../_npm/d3-fetch@3.0.1/b4e2ad9a.js">
<link rel="modulepreload" href="../_npm/d3-force@3.0.0/5e804d15.js">
<link rel="modulepreload" href="../_npm/d3-format@3.1.0/86074ef6.js">
<link rel="modulepreload" href="../_npm/d3-geo@3.1.1/40599fb3.js">
<link rel="modulepreload" href="../_npm/d3-hierarchy@3.1.2/e49e792c.js">
<link rel="modulepreload" href="../_npm/d3-interpolate@3.0.1/8d1e5425.js">
<link rel="modulepreload" href="../_npm/d3-path@3.1.0/20d3f133.js">
<link rel="modulepreload" href="../_npm/d3-polygon@3.0.1/7553081f.js">
<link rel="modulepreload" href="../_npm/d3-quadtree@3.0.1/0dfd751c.js">
<link rel="modulepreload" href="../_npm/d3-random@3.0.1/3c90ee06.js">
<link rel="modulepreload" href="../_npm/d3-scale@4.0.2/843b6a76.js">
<link rel="modulepreload" href="../_npm/d3-scale-chromatic@3.1.0/ba24c2e7.js">
<link rel="modulepreload" href="../_npm/d3-selection@3.0.0/4d94e5b7.js">
<link rel="modulepreload" href="../_npm/d3-shape@3.2.0/6d3a6726.js">
<link rel="modulepreload" href="../_npm/d3-time@3.1.0/9f03c579.js">
<link rel="modulepreload" href="../_npm/d3-time-format@4.1.0/07c9626f.js">
<link rel="modulepreload" href="../_npm/d3-timer@3.0.1/b58a267d.js">
<link rel="modulepreload" href="../_npm/d3-transition@3.0.1/004da2ac.js">
<link rel="modulepreload" href="../_npm/d3-zoom@3.0.0/b5786b3f.js">
<link rel="modulepreload" href="../_npm/binary-search-bounds@2.0.5/cbf6ba23.js">
<link rel="modulepreload" href="../_npm/internmap@2.0.3/e08981d9.js">
<link rel="modulepreload" href="../_npm/delaunator@5.0.1/02d43215.js">
<link rel="modulepreload" href="../_npm/robust-predicates@3.0.2/aa00730b.js">
<link rel="icon" href="../_file/observable.1af93621.png" type="image/png" sizes="32x32">
<script type="module">

This page is where you can iterate. Follow the lab instructions in the [readme.md](/README.md).
[];

🐝 Pollinator Activity Dashboard
Data Context

This dashboard analyzes June 2024 field observations from a local farm to understand bee behavior, flower nectar yield, and weather impacts on pollination.



import {define} from "../_observablehq/client.2ebf0c42.js";
import {registerFile} from "../_observablehq/stdlib.73a8ec5a.js";

registerFile("./pollinator_activity_data.csv", {"name":"./pollinator_activity_data.csv","mimeType":"text/csv","path":"../_file/lab_1/pollinator_activity_data.172590cd.csv","lastModified":1761417441314,"size":29868});

define({id: "2dfb7e5e", inputs: ["FileAttachment"], outputs: ["pollinator_activity_data"], body: async (FileAttachment) => {
const pollinator_activity_data = await FileAttachment("./pollinator_activity_data.csv").csv({ typed: true });
return {pollinator_activity_data};
}});

define({id: "5a9fb7dd", inputs: ["Plot","pollinator_activity_data","display"], body: async (Plot,pollinator_activity_data,display) => {
display(await(
Plot.plot({
  height: 500,
  title: "Pollinator Species: Body Mass vs. Wing Span",
  subtitle: "Visualizing distinct physical clusters for Honeybee, Bumblebee, and Carpenter Bee.",
  marginTop: 30,
  grid: true,
  color: {
    domain: ["Honeybee", "Bumblebee", "Carpenter Bee"],
    range: ["#F8C471", "#9B59B6", "#27AE60"], 
    legend: true,
    label: "Pollinator Species"
  },
  x: {
    label: "Average Body Mass (g)",
    domain: [0, 0.6],
    nice: true
  },
  y: {
    label: "Average Wing Span (mm)",
    domain: [15, 50],
    nice: true
  },
  marks: [
    Plot.dot(pollinator_activity_data, {
      x: "avg_body_mass_g",
      y: "avg_wing_span_mm",
      fill: "pollinator_species",
      stroke: "pollinator_species",
      r: 5,
      opacity: 0.7
    }),
    Plot.frame()
  ]
})
))
}});

define({id: "bbc0f479", inputs: ["Plot","conditionX","pollinator_activity_data","display"], body: async (Plot,conditionX,pollinator_activity_data,display) => {
display(await(
Plot.plot({
  title: `Pollinator Visits by ${conditionX.label}`,
  subtitle: `Average visit count variation across different weather conditions by ${conditionX.label.toLowerCase()}.`,
  height: 500,
  grid: true,
  color: {
    legend: true,
    label: "Weather Condition",
    domain: ["Sunny", "Partly Cloudy", "Cloudy"],
    range: ["#F1948A", "#F5B041", "#5DADE2"]
  },
  x: {
    label: conditionX.label,
    nice: true
  },
  y: {
    label: "Average Visit Count",
    nice: true,
    zero: true
  },
  marks: [
    Plot.lineY(
      pollinator_activity_data,
      Plot.binX(
        { y: "mean" },
        {
          x: d => d[conditionX.value],
          y: "visit_count",
          stroke: "weather_condition",
          curve: "natural",
          thresholds: 10
        }
      )
    ),
    Plot.ruleY([0]),
    Plot.frame()
  ]
})
))
}});

define({id: "5549e7f5", inputs: ["view","Inputs"], outputs: ["envOptions","conditionX"], body: (view,Inputs) => {
const envOptions = [
  { label: "Temperature", value: "temperature" },
  { label: "Humidity", value: "humidity" },
  { label: "Wind Speed", value: "wind_speed" }
];

const conditionX = view(Inputs.select(envOptions, {
  label: "Select Environmental Variable:",
  value: envOptions[0],
  format: d => d.label
}));

return {envOptions,conditionX};
}});

define({id: "5b3d38bd", inputs: ["Plot","pollinator_activity_data","display"], body: async (Plot,pollinator_activity_data,display) => {
display(await(
Plot.plot({
  title: "Average Nectar Production by Flower Species",
  subtitle: "Identifying which flower produces the most nectar on average.",
  height: 500,
  marginLeft: 100,
  grid: true,
  x: {
    label: "Average Nectar Production (μL)",
    nice: true
  },
  y: {
    label: "Flower Species"
  },
  color: {
    legend: false,
    range: ["#58D68D", "#AF7AC5", "#F4D03F"]
  },
  marks: [
    Plot.barX(
      pollinator_activity_data,
      Plot.groupY(
        {x: "mean"}, // aggregate mean nectar production
        {
          y: "flower_species",
          x: "nectar_production",
          fill: "flower_species"
        }
      )
    ),
    Plot.ruleX([0])
  ]
})

))
}});

</script>
</head>
<body>
<input id="observablehq-sidebar-toggle" type="checkbox" title="Toggle sidebar">
<label id="observablehq-sidebar-backdrop" for="observablehq-sidebar-toggle"></label>
<nav id="observablehq-sidebar">
  <ol>
    <label id="observablehq-sidebar-close" for="observablehq-sidebar-toggle"></label>
    <li class="observablehq-link"><a href="../">Interactive Data Visualization (Fall 2025)</a></li>
  </ol>
  <details>
    <summary>Lab 0: Getting Started</summary>
    <ol>
    <li class="observablehq-link"><a href="../lab_0/readme">Instructions</a></li>
    <li class="observablehq-link"><a href="../lab_0/">Dashboard</a></li>
    </ol>
  </details>
  <details open class="observablehq-section-active">
    <summary>Lab 1: Prolific Pollinators</summary>
    <ol>
    <li class="observablehq-link"><a href="./readme">Instructions</a></li>
    <li class="observablehq-link observablehq-link-active"><a href="./">Dashboard</a></li>
    </ol>
  </details>
</nav>
<script>{const e=document.querySelector("#observablehq-sidebar"),o=document.querySelector("#observablehq-sidebar-toggle"),r=sessionStorage.getItem("observablehq-sidebar");r?o.checked=r==="true":o.indeterminate=!0;for(const t of document.querySelectorAll("#observablehq-sidebar summary")){const s=t.parentElement;switch(sessionStorage.getItem(`observablehq-sidebar:${t.textContent}`)){case"true":s.open=!0;break;case"false":s.classList.contains("observablehq-section-active")||(s.open=!1);break}}addEventListener("beforeunload",()=>sessionStorage.setItem("observablehq-sidebar-scrolly",`${e.scrollTop}`));const a=sessionStorage.getItem("observablehq-sidebar-scrolly");a!=null&&(e.style.cssText="overflow: hidden;",e.scrollTop=+a,e.style.cssText="");}</script>
<div id="observablehq-center">
<aside id="observablehq-toc" data-selector="h1:not(:first-of-type)[id], h2:first-child[id], :not(h1) + h2[id]">
<nav>
<div>Contents</div>
<ol>
<li class="observablehq-secondary-link"><a href="#data-analysis-summary">Data Analysis Summary</a></li>
<li class="observablehq-secondary-link"><a href="#1-body-mass-and-wing-span-distribution-of-each-pollinator-species">1. Body Mass and Wing Span Distribution of Each Pollinator Species</a></li>
<li class="observablehq-secondary-link"><a href="#2-ideal-weather-conditions-for-pollination-visits">2. Ideal Weather Conditions for Pollination Visits</a></li>
<li class="observablehq-secondary-link"><a href="#3-mean-nectar-production-by-flower-species">3. Mean Nectar Production by Flower Species</a></li>
</ol>
</nav>
</aside>
<main id="observablehq-main" class="observablehq">
<h1 id="pollinator-activity-analysis" tabindex="-1"><a class="observablehq-header-anchor" href="#pollinator-activity-analysis">Pollinator Activity Analysis</a></h1>
<p>This project utilizes observational data to identify key trends in bee behavior, flower productivity, and environmental factors. The insights are crucial for <strong>optimizing farm management</strong> and <strong>enhancing pollination efforts</strong>.</p>
<hr>
<h2 id="data-analysis-summary" tabindex="-1"><a class="observablehq-header-anchor" href="#data-analysis-summary">Data Analysis Summary</a></h2>
<p>The data reveals distinct patterns across pollinator characteristics, weather preferences, and flower nectar production.</p>
<h2 id="1-body-mass-and-wing-span-distribution-of-each-pollinator-species" tabindex="-1"><a class="observablehq-header-anchor" href="#1-body-mass-and-wing-span-distribution-of-each-pollinator-species">1. Body Mass and Wing Span Distribution of Each Pollinator Species</a></h2>
<p>The analysis confirms the expected size classifications:</p>
<ul>
<li><strong>Honeybees</strong> (Small Bees) are tightly clustered around 0.10 body mass and 0.20 wing span.</li>
<li><strong>Bumblebees</strong> (Medium Bees) occupy the range of 0.20$–$0.30 and $30$–$40.</li>
<li><strong>Carpenter Bees</strong> (Large Bees) are the largest, with masses concentrated between 0.35 and $0.55 and wing spans of 40$–$50.</li>
</ul>
<p>The distinct clusters (visualized below) indicate consistent species identification and provide a clear baseline for studying how size influences foraging and flight efficiency.</p>
<div class="observablehq observablehq--block"><!--:2dfb7e5e:--></div>
<div class="observablehq observablehq--block"><observablehq-loading></observablehq-loading><!--:5a9fb7dd:--></div>
<h2 id="2-ideal-weather-conditions-for-pollination-visits" tabindex="-1"><a class="observablehq-header-anchor" href="#2-ideal-weather-conditions-for-pollination-visits">2. Ideal Weather Conditions for Pollination Visits</a></h2>
<p>This analysis examines how <strong>temperature</strong>, <strong>humidity</strong>, and <strong>wind speed</strong> influence pollinator activity under varying weather conditions.</p>
<ul>
<li>
<p>Pollinator visits <strong>increase steadily with temperature</strong>, peaking under <strong>sunny</strong> conditions. Warmer weather enhances foraging activity, likely due to better nectar flow and higher bee mobility.</p>
</li>
<li>
<p>Visits <strong>peak at moderate humidity (65–75%)</strong>, especially in <strong>partly cloudy</strong> weather. Both very dry and very humid conditions reduce activity, suggesting optimal foraging occurs in balanced moisture levels.</p>
</li>
<li>
<p>Pollinator visits <strong>drop sharply as wind speed rises</strong>, with all weather types showing reduced activity beyond moderate breezes. Calm conditions clearly support more stable and efficient pollination.</p>
</li>
</ul>
<div class="observablehq observablehq--block"><observablehq-loading></observablehq-loading><!--:bbc0f479:--></div>
<div class="observablehq observablehq--block"><!--:5549e7f5:--></div>
<h2 id="3-mean-nectar-production-by-flower-species" tabindex="-1"><a class="observablehq-header-anchor" href="#3-mean-nectar-production-by-flower-species">3. Mean Nectar Production by Flower Species</a></h2>
<p>This analysis identifies which <strong>flower species</strong> produce the most nectar on average.</p>
<p>Among the observed flowers, <strong>Sunflowers</strong> show the <strong>highest nectar production</strong>, indicating their strong potential to attract and sustain pollinator visits.</p>
<p><strong>Coneflowers</strong> and <strong>Lavender</strong> follow closely, each contributing consistently to nectar availability across the study plots.</p>
<div class="observablehq observablehq--block"><observablehq-loading></observablehq-loading><!--:5b3d38bd:--></div>
</main>
<footer id="observablehq-footer">
<nav><a rel="prev" href="./readme"><span>Instructions</span></a></nav>
<div>Built with <a href="https://observablehq.com/" target="_blank" rel="noopener noreferrer">Observable</a> on <a title="2025-10-25T18:37:36">Oct 25, 2025</a>.</div>
</footer>
</div>
</body>
</html>
