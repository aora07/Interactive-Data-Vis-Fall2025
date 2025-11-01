---
title: Prolific Pollinators
toc: true
---

const pollinator_csv = FileAttachment("pollinator_activity_data.csv").csv({typed: true});



```js 
Plot.plot({
  y: {
    type: "log",
    domain: [30,300],
    grid: true
  },
  marks: [
    Plot.lineY(aapl, {x: "Date", y: "Close"})
  ]
})
```

