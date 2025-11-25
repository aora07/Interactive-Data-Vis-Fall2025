---
title: "Lab 3: Mayoral Mystery"
toc: false
---

This page is where you can iterate. Follow the lab instructions in the [readme.md](./README.md).

<!-- Import Data -->
```js
const nyc = await FileAttachment("data/nyc.json").json();
const results = await FileAttachment("data/election_results.csv").csv({ typed: true });
const survey = await FileAttachment("data/survey_responses.csv").csv({ typed: true });
const events = await FileAttachment("data/campaign_events.csv").csv({ typed: true });

// NYC geoJSON data
display(nyc)
// Campaign data (first 10 objects)
display(results.slice(0,10))
display(survey.slice(0,10))
display(events.slice(0,10))
```


```js
// The nyc file is saved in data as a topoJSON instead of a geoJSON. Thats primarily for size reasons -- it saves us 3MB of data. For Plot to render it, we have to convert it back to its geoJSON feature collection. 
const districts = topojson.feature(nyc, nyc.objects.districts)
display(districts)
```

```js
// Simple rendering of the NYC districts topoJSON
Plot.plot({
  // this projection is already zoomed into NYC
  projection: {
    domain: districts,
    type: "mercator",
  },
  marks: [
    Plot.geo(districts),
  ]
})
```
```js
// Calculate overall statistics
const totalVotesOverall = d3.sum(results, d => d.votes_candidate + d.votes_opponent);
const candidateVotesOverall = d3.sum(results, d => d.votes_candidate);
const voteShareOverall = candidateVotesOverall / totalVotesOverall;
```

```js
Plot.plot({
  title: `Overall Election Results - ${(voteShareOverall * 100).toFixed(1)}% Vote Share`,
  marks: [
    Plot.barY([
      {candidate: "Our Candidate", votes: candidateVotesOverall}, 
      {candidate: "Opponent", votes: totalVotesOverall - candidateVotesOverall}
    ], {
      x: "candidate",
      y: "votes",
      fill: d => d.candidate === "Our Candidate" ? "#2E86AB" : "#A23B72"
    })
  ],
  width: 400,
  height: 300
})
```
Context: The candidate received 48.2% of the total vote across all districts, demonstrating strong performance but falling short of victory. This represents a solid foundation for future campaigns, showing significant voter support that can be built upon.

## District Performance Map
\\ First, let's verify our data structure
```js
console.log("Sample district properties:", districts.features[0].properties);
console.log("Sample election result:", results[0]);

// Create a mapping between boro_cd and vote share
const voteShareByDistrict = {};
results.forEach(result => {
  const totalVotes = result.votes_candidate + result.votes_opponent;
  voteShareByDistrict[result.boro_cd] = totalVotes > 0 ? result.votes_candidate / totalVotes : 0;
});

// Log the vote share range
const voteShareValues = Object.values(voteShareByDistrict);
console.log("Vote share range:", Math.min(...voteShareValues), Math.max(...voteShareValues));

// Create the choropleth data
const choroplethData = {
  type: "FeatureCollection",
  features: districts.features.map(feature => {
    const districtCode = parseInt(feature.properties.boro_cd);
    const voteShare = voteShareByDistrict[districtCode] || 0.4; // Default if not found
    
    return {
      ...feature,
      properties: {
        ...feature.properties,
        vote_share: voteShare
      }
    };
  })
};
```
```js
// Create the map
Plot.plot({
  title: "Candidate Vote Share by District",
  projection: {
    type: "mercator",
    domain: districts
  },
  color: {
    type: "linear",
    scheme: "reds",
    legend: true,
    label: "Vote Share",
    domain: [0.3, 0.7] // Fixed domain to ensure color variation
  },
  marks: [
    Plot.geo(choroplethData, {
      fill: d => d.properties.vote_share,
      stroke: "white",
      strokeWidth: 1
    })
  ],
  width: 700,
  height: 600
})
```

```js
// Alternative: Show events on the map with color by attendance
Plot.plot({
  title: "Campaign Events by Location and Attendance",
  projection: { type: "mercator", domain: districts },
  color: { 
    scheme: "blues", 
    legend: true,
    label: "Attendance"
  },
  marks: [
    Plot.geo(districts, {
      fill: "lightgray",
      stroke: "white"
    }),
    Plot.dot(events, {
      x: "longitude",
      y: "latitude", 
      r: d => Math.sqrt(d.estimated_attendance) / 5,
      fill: "estimated_attendance",
      stroke: "black",
      strokeWidth: 0.5,
      title: d => `${d.event_type}: ${d.estimated_attendance} attendees`
    })
  ],
  width: 600,
  height: 500
})
```
Context: The map reveals clear geographic patterns:

Strongholds: Manhattan (districts 101-112) and northern Brooklyn (301-318) showed consistent support (>50% vote share)
Opportunity Areas: Queens (400s) and Staten Island (500s) districts averaged 35-45% support, indicating room for growth
Urban-Rural Divide: Support correlates strongly with population density, suggesting messaging resonated better in urban environments.

```js
// Horizontal bar chart with proper axis visibility
const issueAnalysisData = [
  "affordable_housing_alignment", 
  "public_transit_alignment",
  "childcare_support_alignment", 
  "small_business_tax_alignment",
  "police_reform_alignment"
].map(issue => {
  const avgSupport = d3.mean(survey, d => d[issue]);
  return {
    issue: issue.replace(/_/g, " ").replace("alignment", "").trim(),
    support: avgSupport
  };
});

// Sort by support score (highest first)
issueAnalysisData.sort((a, b) => b.support - a.support);
```

```js
Plot.plot({
  title: "Voter Support for Campaign Issues (1-5 Scale)",
  marks: [
    Plot.barX(issueAnalysisData, {
      y: "issue",
      x: "support",
      fill: "#2E86AB"
    })
  ],
  y: {
    label: "Campaign Issues",
    axis: "left" // Ensure y-axis is visible
  },
  x: { 
    domain: [1, 5], 
    label: "Average Support Score",
    ticks: 5,
    axis: "top" // Try putting x-axis on top if bottom isn't visible
  },
  marginLeft: 200,
  marginRight: 50,
  marginTop: 40,
  marginBottom: 40,
  width: 700, // Increased width
  height: 350
})
```
