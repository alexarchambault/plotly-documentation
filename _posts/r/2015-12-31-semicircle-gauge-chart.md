---
title: R Gauge Meter | Examples | Plotly
name: Gauge Meter
permalink: r/gauge-meter/
description: How to make guage meter charts in R with Plotly.
layout: base
thumbnail: thumbnail/bar.jpg
language: r
page_type: example_index
has_thumbnail: true
display_as: chart_type
order: 10
---






##### Outline

We will use `donut` charts with custom colors to create a `semi-circular` gauge meter, such that lower half of the chart is invisible(same color as background).
This `semi-circular` meter will be overlapped on a base donut chart to create the `analog range` of the meter. We will have to rotate the base chart to align the range marks in the edges of meter's section, because by default `Plotly` places them at the center of a pie section.

###### Base Chart (rotated)

To make a `gauge meter` with 5 equally sized sections, we will create 6 sections in the base chart. So that center(position of label) aligns with the edges of each section.


```r
library(plotly)

base_plot <- plot_ly(
  type = "pie",
  values = c(40, 10, 10, 10, 10, 10, 10),
  labels = c("-", "0", "20", "40", "60", "80", "100"),
  rotation = 108,
  direction = "clockwise",
  hole = 0.4,
  textinfo = "label",
  textposition = "outside",
  hoverinfo = "none",
  domain = list(x = c(0, 0.48), y = c(0, 1)),
  marker = list(colors = c('rgb(255, 255, 255)', 'rgb(255, 255, 255)', 'rgb(255, 255, 255)', 'rgb(255, 255, 255)', 'rgb(255, 255, 255)', 'rgb(255, 255, 255)', 'rgb(255, 255, 255)')),
  showlegend = FALSE
)
```

##### Meter Chart

Now we will superimpose out `semi-circular` meter on top of this.
For that, we will also use 6 sections, but one of them will be invisible to form the lower half (colored same as the background).


```r
base_plot <- add_trace(
  base_plot,
  type = "pie",
  values = c(50, 10, 10, 10, 10, 10),
  labels = c("Error Log Level Meter", "Debug", "Info", "Warn", "Error", "Fatal"),
  rotation = 90,
  direction = "clockwise",
  hole = 0.3,
  textinfo = "label",
  textposition = "inside",
  hoverinfo = "none",
  domain = list(x = c(0, 0.48), y = c(0, 1)),
  marker = list(colors = c('rgb(255, 255, 255)', 'rgb(232,226,202)', 'rgb(226,210,172)', 'rgb(223,189,139)', 'rgb(223,162,103)', 'rgb(226,126,64)')),
  showlegend= FALSE
)
```

We are using `rotation` and `direction` parameters to start the sections from 3 o'clock `[rotation=90]` instead of the default value of 12 o'clock `[rotation=0]`.

##### Dial

Now we need a `dial` to show the current position in the meter at a particular time.
`Plotly`'s [path shape](https://plot.ly/r/reference/#layout-shapes-path) can be used for this. A nice explanation of `SVG` path is available [here](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths) by Mozilla.
We can use a `filled triangle` to create our `Dial`.


```r
base_plot <- layout(
  base_plot,
  shapes = list(
    list(
      type = 'path',
      path = 'M 0.235 0.5 L 0.24 0.62 L 0.245 0.5 Z',
      xref = 'paper',
      yref = 'paper',
      fillcolor = 'rgba(44, 160, 101, 0.5)'
    )
  ),
  annotations = list(
    list(
      xref = 'paper',
      yref = 'paper',
      x = 0.23,
      y = 0.45,
      showarrow = FALSE,
      text = '50'
    )
  )
)

base_plot
```

For the `filled-triangle`, the first point `(0.235, 0.5)` is left to the center of meter `(0.24, 0.5)`, the second point `(0.24 0.62)` is representing the current position on the `semi-circle` and the third point `(0.245, 0.5)` is just right to the center.

`M` represents the `'Move'` command that moves cursor to a particular point, `L` is the `'Line To'` command and `Z` represents the `'Close Path'` command. This way, this path string makes a triangle with those three points.

<iframe height="600" id="igraph" scrolling="no" seamless="seamless" src="https://plot.ly/~RPlotBot/2795.embed" width="800" frameBorder="0"></iframe>