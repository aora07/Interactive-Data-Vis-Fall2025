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

## Election Result Analysis

### Overall Performance
```js
// Calculate overall election statistics
const totalVotesOverall = d3.sum(results, d => d.votes_candidate + d.votes_opponent);
const candidateVotesOverall = d3.sum(results, d => d.votes_candidate);
const voteShareOverall = candidateVotesOverall / totalVotesOverall;
const fiftyPercentLine = totalVotesOverall / 2;
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
    }),
    // Add 50% annotation line
    Plot.ruleY([fiftyPercentLine], {
      stroke: "red",
      strokeDasharray: "4,2",
      strokeWidth: 2
    }),
    // Add label for the 50% line
    Plot.text([{x: "Our Candidate", y: fiftyPercentLine, text: "50% Mark", dy: -10}], {
      fontSize: 12,
      fill: "red",
      fontWeight: "bold"
    })
  ],
  width: 400,
  height: 300
})
```
Our candidate received 56.4% of the total vote across all districts, demonstrating strong performance which led to ultimate victory. Despite this, more campaigning and reaching out to communities such as Queens and Staten Island could improve performance. 

## District Performance Map
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
The map reveals clear geographic patterns:

Upper Manhattan, South Bronx and North Brooklyn shows support with most in attendance in these districts, showing that messaging is most effective in urban areas.  Queens and Staten Island show areas of improvement for candidates, as most attendance is low here (except for two outliers in Queens), which show messaging should be modified to better suit suburban as well as urban needs. 

## Voter Sentiment Analysis
```js
// Simple horizontal bar chart with minimal styling
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
}).sort((a, b) => b.support - a.support);
```
```js
// Use a very basic plot configuration
Plot.plot({
  marks: [
    Plot.barX(issueAnalysisData, {
      y: "issue",
      x: "support"
    })
  ],
  y: { axis: "left" },
  x: { axis: "bottom" },
  marginLeft: 300,
  width: 800,
  height: 400
})
```
Voter sentiment reveals clear priorities:

High Support: Affordable housing and public transit - core urban issues driving voter engagement.  
Moderate Support: Childcare support and small business tax policies - solid but not primary motivators.  
Lower Support: Police reform - potentially polarizing issue that may require refined messaging.