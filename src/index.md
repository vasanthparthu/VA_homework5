---
theme: [glacier, sun-faded]
title: Elite Sports Cars EDA Dashboard
toc: false
---

```js
import * as Plot from "https://cdn.jsdelivr.net/npm/@observablehq/plot@0.6/+esm";
```

# 🚗 Elite Sports Cars Dashboard  
**Name**: Vasanth Parthu Aduvala  
**Student ID**: 801370112  

---

```js
let data = await FileAttachment("data/Elite_Sports_Cars_in_Data_2.csv").csv();
```

```js
const brandDropdown = Inputs.select(
  ["All", ...new Set(data.map(d => d.Brand))],
  { label: "Select Brand", value: "All" }
);

const conditionDropdown = Inputs.select(
  ["All", ...new Set(data.map(d => d.Condition))],
  { label: "Select Condition", value: "All" }
);

```


## 📄 About the Dataset  
The dataset contains specifications and pricing for elite sports cars from top global brands such as Bugatti, Ferrari, McLaren, and Tesla.  
It includes:
- **Brand, Year, and Price**
- **Horsepower and Top Speed**
- **Fuel Type and Fuel Efficiency**
- **Emission Data and Country of Origin**

<div class="grid grid-cols-2">
  <div class="card grid-colspan-1">
    <h1>📈 Luxury Car Pricing Trends</h1>
    <br>
    <div id="lineChart"></div>
  </div>

  <div class="card grid-colspan-1">
    <h1>🏎️ Performance vs Price of Each Model</h1>
    <br>
    <div id="dotChart"></div>
  </div>

  <div class="card grid-colspan-2">
    <h1>🌿 Fuel Efficiency and Emissions by Fuel Type</h1>
    <br>
    <div id="barChart"></div>
  </div>
</div>

```js
view(brandDropdown)
view(conditionDropdown)
```

```js
function renderCharts() {
  const filtered = data.filter(d =>
    (brandDropdown.value === "All" || d.Brand === brandDropdown.value) &&
    (conditionDropdown.value === "All" || d.Condition === conditionDropdown.value)
  );

  const lineChart = Plot.plot({
    color: { legend: true },
    marks: [
      Plot.lineY(
        filtered,
        Plot.binX(
          { y: "mean" },
          { x: "Year", y: "Price", stroke: "Brand", tip: true }
        )
      )
    ]
  });

  const dotChart = Plot.plot({
    color: { legend: true },
    marks: [
      Plot.dot(
        filtered,
        Plot.binY(
          { x: "mean" },
          { x: "Horsepower", y: "Price", stroke: "Brand", tip: true }
        )
      )
    ]
  });

  const barChart = Plot.plot({
    color: { legend: true },
    marks: [
      Plot.barY(
        filtered,
        Plot.groupX(
          { y: "mean" },
          { x: "Brand", y: "Fuel_Efficiency", fill: "Fuel_Type", tip: true }
        )
      ),
      Plot.ruleY([0])
    ]
  });

  document.getElementById("lineChart").replaceChildren(lineChart);
  document.getElementById("dotChart").replaceChildren(dotChart);
  document.getElementById("barChart").replaceChildren(barChart);
}

// Initial render
renderCharts();

// Re-render when dropdown changes
brandDropdown.addEventListener("input", renderCharts);
conditionDropdown.addEventListener("input", renderCharts);
```

---

## 💡 Final Insights & Design Rationale

- Used an interactive **brand selector** to allow multi-brand comparisons.
- Employed `binX` and `binY` aggregations to smooth noisy data.
- Linked all visualizations via shared filtering.
- Each chart includes **tooltips for clarity** on hover.
- Insights show clear trade-offs between performance, price, and fuel efficiency across brands.