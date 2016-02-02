---
layout: tutorials
menu: tutorials
title: Introduction to Vega-Lite
permalink: /tutorials/getting_started.html
---

This tutorial will guide you how to write a visualization specification in Vega-Lite.  We will walk you through all main components of Vega-Lite by adding each of them to an example specification one-by-one.  After creating the example visualization, we will also guide you how to embed the final visualization on a web page.

We suggest that you follow along the tutorial by building a visualization in the [online editor](https://vega.github.io/vega-editor/?mode=vega-lite). Extend your specification in the editor as you read through this tutorial. If something does not work as expected, compare your specifications with ones inside this tutorial.

## The Data

Let's say you have a tabular data set with a categorical variable in the first column `a` and a numerical variable in the second column `b`.

| a | b |
|---|---|
| C | 2 |
| C | 7 |
| C | 4 |
| D | 1 |
| D | 2 |
| D | 6 |
| E | 8 |
| E | 4 |
| E | 7 |

We can represent this data as a [JSON array](http://www.json.org/) in which each row is an object in the array.

```json
[
  {"a": "C", "b": 2},
  {"a": "C", "b": 7},
  {"a": "C", "b": 4},
  {"a": "D", "b": 1},
  {"a": "D", "b": 2},
  {"a": "D", "b": 6},
  {"a": "E", "b": 8},
  {"a": "E", "b": 4},
  {"a": "E", "b": 7}
]
```

To visualize this data with Vega-Lite, we can add it directly to the `data` property in a Vega-Lite specification. (Note that we reformatted the array to make it more compact.)

```json
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  }
}
```

The `data` property defines the data source of the visualization.  In this example, we embed the data inline by directly setting `values` property.  Vega-Lite also supports [other types of data sources](data.html) besides inline data as well.  For this tutorial, we will use inline data.  

## Encoding Data with Marks

Now we have a data source but we haven't defined yet how the the data should be visualized.
All graphical elements in Vega-Lite are called *marks*. Marks are associated with data: a mark is rendered once per associated datum.  Variables in the datum can be mapped to visual channels of the mark such as position and color.

To show the data as a point, we can set the `mark` property to `point`.

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point"
}
</div>

Now, we can see a point. In fact, Vega-Lite renders one point for each object in the array, but they are all overlapping.

The important next step is mapping data values to visual channels of the marks via the `encoding` property. For example, to visually separate the points, we can *encode* the variable `a` of the data to `x` channel, which represents the x-location of the points.

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {
    "x": {"field": "a", "type": "ordinal"}
  }
}
</div>

Here, we added an `encoding` object to our specification.

```json
...
"encoding": {
  "x": {"field": "a", "type": "ordinal"}
}
...
```

The `encoding` object is a key-value mapping between encoding channels and definitions of the mapped data fields.  The field definition describes the field's name (`field`) and its [data type]({{site.baseurl}}/docs/encoding.html#types) (`type`).  
In this example, we map the values for field `a` to the *encoding channel* `x` (the x-location of the points) and set `a`'s data type to `ordinal`.  <!-- TODO: explain what is ordinal, and why is this ordinal -->

In the visualization above, Vega-Lite automatically adds an axis with labels for the different categories as well as an axis title. However, 3 points in each category are still overlapping. So far, we have only defined a visual encoding for the field `a`. We can also map the field `b` to the `y` channel.

```json
...
"y": {"field": "b", "type": "quantitative"}
...
```

This time we set the field type to be `quantitative` because the values in field `b` are numeric.

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {
    "x": {"field": "a", "type": "ordinal"},
    "y": {"field": "b", "type": "quantitative"}
  }
}
</div>

Now we can see the raw data points. Note that Vega-Lite automatically adds grid lines to the y-axis to help you compare the magnitude of the `b` values.

## Data Transformation: Aggregation

Vega-Lite also supports data transformation such as aggregation. By adding `"aggregate": "mean"` to the definition of the `y` channel, we can see the average value of `a` in each category. For example, the average value of category `D` is `(1 + 2 + 6)/3 = 9/3 = 3`.

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {
    "x": {"field": "a", "type": "ordinal"},
    "y": {"field": "b", "type": "quantitative", "aggregate": "mean"}
  }
}
</div>

Great! You computed the aggregate values for each category and visualized the resulting value as a point. Typically aggregated values for categories are visualized using bar charts. To create a bar chart, we have to change the mark type from `point` to `bar`.


```diff
- "mark": "point"
+ "mark": "bar"
```

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "bar",
  "encoding": {
    "x": {"field": "a", "type": "ordinal"},
    "y": {"field": "b", "type": "quantitative", "aggregate": "mean"}
  }
}
</div>

Since the quantitative value is on y, you automatically get a vertical bar chart. We can change the visualization to a horizontal bar chart by swapping the `x` and `y` channels.

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "bar",
  "encoding": {
    "y": {"field": "a", "type": "ordinal"},
    "x": {"field": "b", "type": "quantitative", "aggregate": "mean"}
  }
}
</div>

## Customize your Visualization

<!-- TODO need to find a way to talk about conciseness here somehow. -->
Vega-Lite automatically provides default properties for the visualization. You can further customize these values by adding more properties. For example, to change the title of the x-axis from `MEAN(b)` to `average of b`, we can set the title property of the axis in the `x` channel.

<div class="vl-example">
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "bar",
  "encoding": {
    "y": {"field": "a", "type": "ordinal"},
    "x": {
      "field": "b", "type": "quantitative", "aggregate": "mean",
      "axis": {
        "title": "average of b"
      }
    }
  }
}
</div>


## [](#embed) Publish your Visualization Online

You have learned about basic components of a Vega-Lite specification.
Now, let's see how to publish your visualization.

To embed your visualization on a website, you can create a web page with the following content:

```html
<!DOCTYPE html>
<head>
  <title>Vega Lite Bar Chart</title>
  <meta charset="utf-8">

  <script src="//d3js.org/d3.v3.min.js"></script>
  <script src="//vega.github.io/vega/vega.js"></script>
  <script src="//vega.github.io/vega-lite/vega-lite.js"></script>
  <script src="//vega.github.io/vega-editor/vendor/vega-embed.js" charset="utf-8"></script>

  <style media="screen">
    /* Add space between vega-embed links  */
    .vega-actions a {
      margin-right: 5px;
    }
  </style>
</head>
<body>
  <!-- Container for the visualization -->
  <div id="vis"></div>

  <script>
  var vlSpec = {
    "data": {
      "values": [
        {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
        {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
        {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
      ]
    },
    "mark": "bar",
    "encoding": {
      "y": {"field": "a", "type": "ordinal"},
      "x": {
        "field": "b", "type": "quantitative", "aggregate": "mean",
        "axis": {
          "title": "average of b"
        }
      }
    }
  };

  var embedSpec = {
    mode: "vega-lite",  // Instruct Vega-Embed to use the Vega-Lite compiler
    spec: vlSpec
  };

  // Embed the visualization in the container with id `vis`
  vg.embed("#vis", embedSpec, function(error, result) {
    // Callback receiving the View instance and parsed Vega spec
    // result.view is the View, which resides under the '#vis' element
  });
  </script>
</body>
</html>
```

In this webpage, we first load the dependencies for Vega-Lite (D3, Vega-Embed, Vega, and Vega-Lite) in the `<head/>` tag of the document. We also create an HTML `<div/>` element with id `vis` to serve as a container for the visualization.  

In the JavaScript code, we create a variable `vlSpec` that holds the Vega-Lite specification in JSON format. The `vl.embed` method translates a Vega-Lite specification into a Vega specification and then calls the [Vega Runtime](https://github.com/vega/vega/wiki/Runtime) to display visualization in the container `<div/>` element.

If viewed in a browser, this page displays our bar chart. You can also see it [here]({{site.baseurl}}/site/demo.html).

## Next Steps

Now you can create a website that embeds a Vega-Lite specification.  If you want to learn more about Vega-Lite, please feel free to:

- Read the [next tutorial]({{site.baseurl}}/tutorials/weather.html).
- See the [examples gallery]({{site.baseurl}}/gallery.html).
- Build your own visualizations in the [online editor](https://vega.github.io/vega-editor/?mode=vega-lite).
- Browse through the [documentation]({{site.baseurl}}/docs/).