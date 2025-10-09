---
title: "Lab 0:Getting Started"
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
    <td>01/01/90</td>
    <td>123 Apple Lane, Strawberry Land, 10000</td>
</tr>
<tr>
    <td>Jane Doe</td>
    <td>02/14/87</td>
    <td>555 Sherbet St, Strawberry Land, 10000</td>
</tr>
<tr>
    <td>John Doe</td>
    <td>12/25/01</td>
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

```js
    viewof replay = Inputs.button("Replay")
    progress = {
  replay;
  const width = 360;
  const height = 20;
  const context = DOM.context2d(width, height);
  context.canvas.style.border = "solid 1px black";
  for (let i = width; i >= 0; --i) {
    context.clearRect(0, 0, width, height);
    context.fillRect(0, 0, i, height);
    yield context.canvas;
  }
}
```