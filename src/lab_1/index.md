---
title: "Lab 1: Passing Pollinators"
toc: true
---

This dashboard uses the field study dataset in `/data/pollinator_activity_data.csv` to answer:

1. **What is the body mass and wing span distribution of each pollinator species?**  
2. **What is the ideal weather for pollinating (conditions, temperature, etc.)?**  
3. **Which flower has the most nectar production?**

## 1. Body Mass and Wing Span Distribution of Each Pollinator Species

The graph shows honeybees clustered around 0-25mm in average wing span and 0.1g in average body mass. Bumblebees vary greatly from 25mm-42mm in average wingspan and cluster from 0.2-0.3g in average body mass. Finally, carpenter bees average around 0.4-0.5g in bodt mass, and 34-50 mm in average wing span. 

```js
const pollinator_activity_data = await FileAttachment("./data/pollinator_activity_data.csv").csv({ typed: true });
```

```js
Plot.plot({
  height: 500,
  title: "Pollinator Species: Body Mass vs. Wing Span",
  marginTop: 30,
  grid: true,
  color: {
    domain: ["Honeybee", "Bumblebee", "Carpenter Bee"],
    range: ["#FFD603", "#000000", "#8CCDDB"], 
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
```


## 2. Ideal Weather Conditions for Pollination Visits

This chart shows that pollinators are most likely to visit when it is sunny, and their visits increase steadily under increasing temperatures. Also, it peaks in moderate humidity (65-75%) under partly cloudy weather conditions. Also, the pollinator increase their visits with the decrease of wind speeds. 

```js
Plot.plot({
  title: `Pollinator Visits by ${conditionX.label}`,
  height: 500,
  grid: true,
  color: {
    legend: true,
    label: "Weather Condition",
    domain: ["Sunny", "Partly Cloudy", "Cloudy"],
    range: ["#FFD603", "#000000", "#8CCDDB"]
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
```
```js
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

```




## 3. Mean Nectar Production by Flower Species

This graph shows which flower species creates more nectar on average. On average, Sunflowers produce the highest nectar production. 

```js
Plot.plot({
  title: "Average Nectar Production by Flower Species",
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
    range: ["#FFD603", "#000000", "#8CCDDB"]
  },
  marks: [
    Plot.barX(
      Plot.groupY(
        { x: "mean" },             // ✅ reducer
        pollinator_activity_data,   // ✅ dataset in correct place
        {
          y: "flower_species",
          x: "nectar_production",
          fill: "flower_species",
          stroke: "black",
          strokeWidth: 1.5
        }
      )
    ),
    Plot.ruleX([0])
  ]
});


```