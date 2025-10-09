---
title: "Lab 0: Getting Started"
toc: true
---
# Lab 0: Getting Started
## This is our first lab in this class

Hello world! This is the first lab submission for this class. I am using a markdown file provided for a previous class to aid in remmebering necessary elements in working with markdown, html, and javascript.

<table>
<tr>
    <th>Patient Name</th>
    <th>D.O.B</th>
    <th>Address</th>
</tr>
<tr>
    <td>John Smith</td>
    <td>Jane Doe</td>
    <td>John Doe</td>
</tr>
<tr>
    <td>123 Apple Lane, Strawberry Land, 10000</td>
    <td>555 Sherbet St, Strawberry Land, 10000</td>
    <td>991 Rocky Road, Strawberry Land, 10000</td>
</tr>
</table>

### Villains
<ul>
    <li>Custard the Cat</li>
    <li>Purple Pieman</li>
    <li>Sour Grapes</li>
</ul>

<img src="https://static.wikia.nocookie.net/strawberryshortcake/images/b/b9/Inaccurate_map_of_strawberryland.png/revision/latest?cb=20210430182300" alt="Strawberryland" style="width:104px;height:142px;">

const cities = [
  { city: "New York", population: 8398748 },
  { city: "Los Angeles", population: 3990456 },
  { city: "Chicago", population: 2705994 },
  { city: "Houston", population: 2320268 },
  { city: "Phoenix", population: 1680992 },
];

// Create a reactive variable `filterText` from a text input
const filterText = view(Inputs.text({
  label: "Filter by city",
  placeholder: "e.g., Chicago"
}));

// Automatically re-filter cities whenever the input changes
const filteredCities = cities.filter(d => 
  d.city.toLowerCase().includes(filterText.toLowerCase())
);

# Interactive City Filter

${view(Inputs.text({ label: "Filter by city", placeholder: "e.g., Chicago" }))}

```js
const cities = [
  { city: "New York", population: 8398748 },
  { city: "Los Angeles", population: 3990456 },
  { city: "Chicago", population: 2705994 },
  { city: "Houston", population: 2320268 },
  { city: "Phoenix", population: 1680992 },
];

const filterText = view(Inputs.text({ label: "Filter by city" }));

const filteredCities = cities.filter(d => 
  d.city.toLowerCase().includes(filterText.toLowerCase())
);

display(filteredCities);
```
