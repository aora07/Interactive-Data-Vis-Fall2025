<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Pollinator Dashboard | Interactive Data Visualization</title>

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
  <div id="plot-mass" class="plot"></div>
</section>

<section>
  <h2>2. Ideal Weather Conditions for Pollination</h2>
  <div id="plot-weather" class="plot"></div>
</section>
