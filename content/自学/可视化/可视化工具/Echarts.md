---
publish: true
created: 2026-08-03T07:05:34.171Z
modified: 2026-09-14T11:33:14.777Z
---

- 通过一个庞大的配置项对象（`option`）来定义图表的方方面面，官方提供了覆盖绝大多数场景的配置。
- 咋这多🤔还是用到的时候再来看🤔
- [示例的网页](https://echarts.apache.org/examples/zh/index.html#chart-type-line)
  具体的代码
- 全部都用option={}包了
- 坐标轴xAxis, yAxis，x可以有type和data，y数据单独分成series（可以有很多个）
- 【平滑折线图】：smooth: true
- 【面积图】：可以加一个areaStyle，甚至可以渐变填色

```json
areaStyle: {        
	color: {
	  type: 'linear', // 线性渐变
	  x: 0,
	  y: 0,
	  x2: 0,
	  y2: 1, // 从顶部 (y=0) 到底部 (y=1) 渐变
	  colorStops: [
		{ offset: 0, color: '#5470c6' }, // 顶部颜色（深蓝）
		{ offset: 1, color: '#91cc75' }  // 底部颜色（浅绿）
	  ]
	}
  }
```

# 【堆叠面积图】

stack：'一个随便起什么的名字'，相同stack名字的series会互相堆叠
\- emphasis：悬停聚焦的数据是什么？

- 【tooltip】鼠标悬停时的浮窗

```json
  tooltip: {
    trigger: 'axis',//触发方式设置为坐标轴触发
    axisPointer: {//指示器样式设为十字准星。你移动鼠标时，图表上会出现一条垂直和一条水平交叉的参考线
      type: 'cross',
      label: {//十字准星两端附带的小标签
        backgroundColor: '#6a7985'
      }
    }
  }
```

- 【toolbox】它是图表左上角（默认位置）的一个浮动工具栏，提供内置的实用功能按钮

```json
  toolbox: {
    feature: {
      saveAsImage: {}
    }
  }
```

- 【boundaryGap】意思是坐标轴和两边的边界，如果是true就会间隔一小段
- 【showSymbol】数据点标记是否显示，比如对应数据上的小圆圈
- 【opacity】: 0.8 —— 透明度：在堆叠图中，这会让底层（下方）的颜色微微透上来，让 5 个色块叠加的区域产生自然的色彩混合效果，而不是生硬地遮挡住下面的色块，视觉上会更高级、更融合。
- 【渐变填充】color: new echarts.graphic.LinearGradient(...)
  (0, 0, 0, 1)
  前两个 (0, 0) = 起点在左上角（x=0, y=0）。
  后两个 (0, 1) = 终点在左下角（x=0, y=1）。
  结论：这是一个垂直渐变（从上往下）。

```json
  areaStyle: {
	opacity: 0.8,
	color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
	  {
		offset: 0,
		color: 'rgb(128, 255, 165)'
	  },
	  {
		offset: 1,
		color: 'rgb(1, 191, 236)'
	  }
	])
  }
```

# 函数

```json
  grid: {
    top: 40,
    left: 50,
    right: 40,
    bottom: 50
  }
```

就是图像距离上下左右的边界🤔这里距离bottom和left都要大一点，因为要放置坐标轴

```json
minorTick: {
  show: true
},
minorSplitLine: {
  show: true
}
```

显示小刻度和分割线
【clip】true：裁剪溢出部分，让函数图像不要超出容器

```json
  dataZoom: [
    {
      show: true,
      type: 'inside',//内置型缩放。没有可见的滚动条滑块，完全靠鼠标操作（在图表区域滚动滚轮进行缩放，按住拖拽进行平移）
      filterMode: 'none',//缩放时只改变显示范围，绝不改变原始数据
      xAxisIndex: [0],//明确指定只控制第 1 个 X 轴和第 1 个 Y 轴。
      startValue: -20,
      endValue: 20
    },
    {
      show: true,
      type: 'inside',
      filterMode: 'none',
      yAxisIndex: [0],
      startValue: -20,
      endValue: 20
    }
  ]
```

缩放时候的参数
