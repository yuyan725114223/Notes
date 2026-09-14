---
publish: true
created: 2026-08-03T05:35:10.551Z
modified: 2026-09-14T11:33:07.858Z
---

【数据类型】tick：刻度
【bin】：true的时候开始分桶，默认10个桶
也可以通过更改step来变化相邻桶之间的距离，比如

```json
"x": {
  "bin": { "step": 1 },  // 表示每 1 个单位降水量作为一个区间
  "field": "precipitation",
  "type": "quantitative"
}
```

当我们将类型设置为`temporal`时，Vega-Lite原生支持日期和日期离散化。例如，在下面的图中，我们总结了每个月的降水量。为了将数据离散化为月份，我们设置了\`"timeUnit": "month"
这个意思就是把不同的月份每个划分成一个桶，如果是yearmonth不同的年份就算不同的桶，如果是month算同一个桶
【aggregate】函数更改：类型有max，count，min，average/mean（其实没啥区别）
【坐标标题】

```json
"axis": {"title": "Month of the year"}
```

【transform】一种计算中间量的方法

```json
{ "data": {"url": "data/seattle-weather.csv"}, "transform": [
 {"calculate": "datum.temp_max - datum.temp_min", "as": "temp_range"} 
 ], 
 "mark": "line", 
 "encoding": { "x": { "timeUnit": "month", "field": "date", "type": "temporal" }, 
 "y": { "aggregate": "mean", "field": "temp_range", "type": "quantitative" } } }
```

算出来之后 按正常的已经有的变量来看使用
【堆叠条形图】可以通过增加颜色通道；选择自定义的调色板来使数据更好看

```json
"color": { "field": "weather", "type": "nominal" }
```

![[图源/图源2/Pasted image 20260803144216.png|300]]
【自定义调色板】在color里设，上下是一一对应的

```json
"scale": { 
"domain": ["sun", "fog", "drizzle", "rain", "snow"], 
"range": ["#e7ba52", "#c7c7c7", "#aec7e8", "#1f77b4", "#9467bd"] 
}
```

【legend】图例
[Vegalite的官方图示例](https://vega.github.io/vega-lite/examples/)
[Vegalite的在线编辑器网址](https://vega.github.io/editor/#/edited)
