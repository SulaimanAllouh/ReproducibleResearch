---
title: "Plotly Plot Example"
author: "Your Name"
date: "`r Sys.Date()`"
output: html_document
---

## Plotly Plot

This is an example of a Plotly plot created in R.

```{r setup, include=FALSE}
library(plotly)

# Create a sample plot using Plotly
data <- data.frame(
  x = rnorm(100),
  y = rnorm(100)
)

plot_ly(data, x = ~x, y = ~y, type = 'scatter', mode = 'markers')
```
