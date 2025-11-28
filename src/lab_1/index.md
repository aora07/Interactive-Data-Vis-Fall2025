---
title: "Lab 1: Passing Pollinators"
toc: true
---

# Pollinator Activity Dashboard

This dashboard analyzes pollinator activity data to answer key questions about bee species characteristics, ideal weather conditions for pollination, and nectar production by flower species.

```js
// Load and prepare the data
const data = await FileAttachment("./data/pollinator_activity_data.csv").csv({typed: true});
```

## 1. Body Mass and Wing Span Distribution by Pollinator Species
This visualization shows the physical characteristics of the three observed bee species.

// Question 1: Body mass and wing span distribution
```js 
function chart1() {
  return Plot.plot({
    title: "Body Mass and Wing Span Distribution by Species",
    grid: true,
    marginLeft: 80,
    facet: {
      data: data,
      x: "pollinator_species",
      marginRight: 40
    },
    marks: [
      Plot.ruleX([0]),
      Plot.dot(data, {
        x: "avg_body_mass_g",
        y: "avg_wing_span_mm",
        fill: "pollinator_species",
        stroke: "black",
        strokeWidth: 0.5,
        r: 3
      }),
      Plot.linearRegressionY(data, {
        x: "avg_body_mass_g",
        y: "avg_wing_span_mm",
        stroke: "gray"
      })
    ],
    x: {
      label: "Average Body Mass (g)",
      domain: [0.05, 0.55]
    },
    y: {
      label: "Average Wing Span (mm)",
      domain: [15, 50]
    },
    color: {
      legend: true
    },
    width: 800,
    height: 300
  });
}
display(chart1());
```

## 2. Ideal Weather Conditions for Pollinating
This analysis examines how weather conditions affect pollinator activity.
```js
// Question 2: Ideal weather conditions for pollinating
function chart2() {
  return Plot.plot({
    title: "Pollinator Activity by Temperature and Weather Condition",
    grid: true,
    marks: [
      Plot.dot(data, {
        x: "temperature",
        y: "visit_count",
        fill: "weather_condition",
        stroke: "black",
        strokeWidth: 0.5,
        r: 4
      }),
      Plot.linearRegressionY(data, {
        x: "temperature",
        y: "visit_count",
        stroke: "gray"
      })
    ],
    x: {
      label: "Temperature (°C)",
      domain: [14, 32]
    },
    y: {
      label: "Number of Visits",
      domain: [0, 30]
    },
    color: {
      legend: true
    },
    width: 800,
    height: 400
  });
}

display(chart2());

```
```js
// Weather condition vs visit count
function chart2b() {
  return Plot.plot({
    title: "Average Visits by Weather Condition",
    marks: [
      Plot.barY(data, {
        x: "weather_condition",
        y: "visit_count",
        fill: "weather_condition"
      }),
      Plot.ruleY([0])
    ],
    x: {label: "Weather Condition"},
    y: {label: "Average Number of Visits"},
    color: {legend: true},
    width: 800,
    height: 300
  });
}

display(chart2b());

```
```js
// Temperature range analysis
function chart2c() {
  return Plot.plot({
    title: "Pollinator Activity by Temperature Ranges",
    marks: [
      Plot.rectY(data, Plot.binX({y: "count"}, {x: "temperature", fill: "pollinator_species"})),
      Plot.ruleY([0])
    ],
    x: {label: "Temperature (°C)"},
    y: {label: "Number of Observations"},
    color: {legend: true},
    width: 800,
    height: 300
  });
}

display(chart2c());
```

## 3. Nectar Production by Flower Species
This section compares nectar production across different flower species.
```js
// Question 3: Nectar production by flower species
function chart3() {
  return Plot.plot({
    title: "Nectar Production by Flower Species",
    marks: [
      Plot.boxX(data, {
        x: "nectar_production",
        y: "flower_species",
        fill: "flower_species"
      }),
      Plot.dot(data, {
        x: "nectar_production",
        y: "flower_species",
        stroke: "black",
        strokeWidth: 0.5
      })
    ],
    x: {
      label: "Nectar Production (μL)",
      domain: [0.3, 1.1]
    },
    y: {label: "Flower Species"},
    color: {legend: true},
    width: 800,
    height: 200
  });
}

display(chart3());
```

```js
// Additional view: Average nectar production
function chart3b() {
  return Plot.plot({
    title: "Average Nectar Production by Flower Species",
    marks: [
      Plot.barY(data, {
        x: "flower_species",
        y: "nectar_production",
        fill: "flower_species"
      }),
      Plot.ruleY([0])
    ],
    x: {label: "Flower Species"},
    y: {label: "Average Nectar Production (μL)"},
    color: {legend: true},
    width: 800,
    height: 300
  });
}

display(chart3b());
```

## Summary of Findings
### 1. Bee Species Characteristics
Honeybees: Smallest body mass (0.08-0.12g) and wing span (15-22mm).  
Bumblebees: Medium size with body mass around 0.22-0.27g and wing span 30-38mm.  
Carpenter Bees: Largest species with body mass 0.38-0.52g and wing span 38-48mm.  
### 2. Ideal Weather Conditions
Temperature: Optimal range appears to be 20-28°C.  
Weather Conditions: Sunny and partly cloudy conditions show highest pollinator activity.  
Peak Activity: Most visits occur in moderate temperatures with low to moderate humidity.  
### 3. Nectar Production
Sunflowers: Highest nectar production (average ~0.95μL).  
Coneflowers: Moderate nectar production (average ~0.64μL).  
Lavender: Lowest nectar production (average ~0.54μL).  