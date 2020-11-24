---
title: D3 and Vega Lite Introduction
date: 2020-11-04 10:25
description: First week to learn how to use D3.js and Vega-Lite
categories:
 - 前端
 - 数据可视化
---

## HighLight

- [Awesome D3](https://github.com/wbkd/awesome-d3)
- [Awesome Vega](https://github.com/joaopalmeiro/awesome-vega)

## The Basics of D3.js

**The origin of the library:**

M. Bostock, V. Ogievetsky and J. Heer, "D³ Data-Driven Documents," in IEEE Transactions on Visualization and Computer Graphics, vol. 17, no. 12, pp. 2301-2309, Dec. 2011, doi: 10.1109/TVCG.2011.185.

[![B6Lchd.md.png](https://s1.ax1x.com/2020/11/04/B6Lchd.md.png)](https://imgchr.com/i/B6Lchd)

### Overview

使用 JavaScript 修改 DOM 元素的传统方法:

```JavaScript
var paragraphs = document.getElementsByTagName("p");
for (var i = 0; i < paragraphs.length; i++) {
  var paragraph = paragraphs.item(i);
  paragraph.style.setProperty("color", "blue", null);
}
```

使用 D3 达到同样的效果

```JavaScript
d3.selectAll("p").style("color", "blue");
```

[d3-selection](https://github.com/d3/d3-selection) 对 DOM 元素进行了封装，支持方法链，接口更加友好

[数据绑定](https://bost.ocks.org/mike/join/)是 D3 的核心功能

[![B6XdeK.md.png](https://s1.ax1x.com/2020/11/04/B6XdeK.md.png)](https://imgchr.com/i/B6XdeK)

```JavaScript
d3.select("body")
  .selectAll("p")
  .data([4, 8, 15, 16, 23, 42])
  .enter().append("p")
    .text(function(d) { return "I’m number " + d + "!"; });
```

D3 包含多个模块，可单独使用，提供了强大的可视化能力

## The Basics of Vega-Lite

**The origin of the library:**

A. Satyanarayan, D. Moritz, K. Wongsuphasawat and J. Heer, "Vega-Lite: A Grammar of Interactive Graphics," in IEEE Transactions on Visualization and Computer Graphics, vol. 23, no. 1, pp. 341-350, Jan. 2017, doi: 10.1109/TVCG.2016.2599030.

[![B6jXjI.md.png](https://s1.ax1x.com/2020/11/04/B6jXjI.md.png)](https://imgchr.com/i/B6jXjI)

### Overview

Vega-Lite 使用声明式语法

```json
{
  "data": {"url": "data/seattle-weather.csv"},
  "layer": [{
    "selection": {
      "brush": {
        "type": "interval",
        "encodings": ["x"]
      }
    },
    "mark": "bar",
    "encoding": {
      "x": {
        "timeUnit": "month",
        "field": "date",
        "type": "ordinal"
      },
      "y": {
        "aggregate": "mean",
        "field": "precipitation",
        "type": "quantitative"
      },
      "opacity": {
        "condition": {
          "selection": "brush", "value": 1
        },
        "value": 0.7
      }
    }
  }, {
    "transform": [{
      "filter": {"selection": "brush"}
    }],
    "mark": "rule",
    "encoding": {
      "y": {
        "aggregate": "mean",
        "field": "precipitation",
        "type": "quantitative"
      },
      "color": {"value": "firebrick"},
      "size": {"value": 3}
    }
  }]
}
```

[![B6vyrt.png](https://s1.ax1x.com/2020/11/04/B6vyrt.png)](https://imgchr.com/i/B6vyrt)

- Data: 表格型数据
- Mark: 可视化元素
- Encoding: 数据到可视化的映射
- Transform: 数据转换(聚合、过滤等操作)
- View Composition: 多视图
- Selection: 交互

[Vega-Embed](https://github.com/vega/vega-embed): Vega-Lite 与 D3, Echarts 等一起使用

## Comparison

https://vega.github.io/vega/about/vega-and-d3/

## References

- [D3 Official Website](https://d3js.org/)
- [Vega-Lite Offical Website](https://vega.github.io/vega-lite/)
