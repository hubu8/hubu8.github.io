# Echarts之3D柱状图组件封装


<!--more-->

### 0、最终实现效果

![image-20230407091148921](/前端/image-20230407091148921.png)

### 1、组件代码

#### 1.1、Vue组件代码

```vue
<template>
  <div :id="id" style="width: 100%;height: 100%;padding: 10px;"></div>
</template>

<script>
import * as echarts from 'echarts'
import chartUtils from './chartUtils'
import cubeConst from './3dcubeConst'

export default {
  name: 'echarts-bar-3d',
  props: {
    // id 容器id,可以不传
    id: {
      type: String,
      default: 'myChart' + Math.ceil(Math.random() * 10)
    },
    // x轴数据
    xData: {
      type: Array,
      default: () => {
        return []
      }
    },
    // y轴数据
    yData: {
      type: Array,
      default: () => {
        return []
      }
    },
    lineData: {
      type: Array,
      default: () => {
        return []
      }
    },
    // 颜色
    colors: {
      type: Array,
      default: () => {
        return []
      }
    },
    // 显示模式
    mode: {
      type: String,
      // vertical 垂直
      // horizontal 水平
      default: 'vertical'
    },
    coverYAxis: {
      type: Object,
      default: () => {
        return {}
      }
    },
    coverXAxis: {
      type: Object,
      default: () => {
        return {}
      }
    },
    coverTooltip: {
      type: Object,
      default: () => {
        return {}
      }
    },
    extraSeries: {
      type: Array,
      default: () => {
        return []
      }
    },
    extraYAxis: {
      type: Array,
      default: () => {
        return []
      }
    },
    extraOption: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  watch: {
    yData: function(val) {
      if (
        val &&
        val.length > 0 &&
        this.xData &&
        this.xData.length > 0 &&
        this.colors &&
        this.colors.length > 0
      ) {
        console.log("135")
        this.initChart()
      }else{
        console.log("125")
      }
    }
  },
  mounted(){
    this.initChart();
  },
  methods: {
    initChart() {
      console.log(cubeConst)
      // 注册自定义3D柱状图
      chartUtils.registerShapes()

      const isVertical = this.mode == 'vertical'
      // 数据转换
      const yData = isVertical ? chartUtils.transferData(this.yData) : this.yData
      console.log(this.colors)
      // 根据数据生成series系列
      let series = chartUtils.generateSeries(yData, this.colors, this.mode)
      console.log(series)
      const option = {
        animation: false,
        grid: {
          show: true,
          left: 20,
          right: 40,
          top: '10%',
          bottom: '10%',
          containLabel: true
        },
        tooltip: {
          show: true,
          trigger: 'axis',
          axisPointer: {
            type: 'shadow'
          },
          textStyle: {
            color: '#04a19e'
          },
          backgroundColor: 'rgba(0,0,0,0.5)',
          borderColor: 'rgba(0,0,0,0.3)',
          formatter: params => {
            let result =
              '<div style="line-height:1;font-size: var(--cdp-dashboard-text-size);">' +
              params[0].name +
              '<br/>'
            for (let i = 0; i < params.length; i++) {
              let value = params[i].value
              if (isVertical && i > 0) {
                value -= params[i - 1].value
              }
              result +=
                '<div style="margin: 10px 0 0;">' +
                params[i].marker +
                `<span style="font-size: var(--cdp-dashboard-text-size);text-align: left;margin-left:2px;margin-right: 20px;color: #04a19e">${params[i].seriesName}</span>` +
                `<span style="font-size: var(--cdp-dashboard-text-size);float: right;font-weight: bold;color: #04a19e">
                    ${value}${this.coverTooltip.unit || ''}</span>` +
                '</div>'
            }
            return result + '</div>'
          },
          ...this.coverTooltip
        },
        xAxis: {
          data: this.xData,
          axisLine: {
            // 坐标轴线
            // show: true,
            lineStyle: {
              color: 'rgba(0, 168, 168, 1)',
              shadowBlur: 10,
              shadowOffsetX: 10,
              shadowOffsetY: -10,
              shadowColor: 'rgb(0,9,9)',
            }
          },
          axisTick: {
            // 坐标轴刻度
            show: false
          },
          axisLabel: {
            fontSize: cubeConst.fontSize,
            interval: 0,
            color: 'rgba(0, 168, 168, 1)'
          },
          ...this.coverXAxis
        },
        yAxis: [
          {
            type: 'value',
            name: '',
            splitNumber: 6,
            nameTextStyle: {
              fontSize: cubeConst.fontSize,
              color: 'rgba(0, 168, 168, 1)'
            },
            axisLine: {
              show: true,
              lineStyle: {
                color: 'rgba(0, 168, 168, 1)'
              }
            },
            splitLine: {
              show: true,
              lineStyle: {
                color: 'rgba(0, 168, 168, 1)'
              }
            },
            // 坐标轴刻度
            axisTick: {
              show: false
            },
            axisLabel: {
              fontSize: cubeConst.fontSize,
              color: 'rgba(0, 168, 168, 1)'
            },
            boundaryGap: ['20%', '20%'],
            ...this.coverYAxis
          },
          ...this.extraYAxis
        ],
        series: [...series, ...this.extraSeries]
      }
      let cubeEchart = document.getElementById(this.id)
      let offsetWidth = cubeEchart.parentNode.offsetWidth
      cubeEchart.style.width = offsetWidth+"px";
      let myChart = echarts.init(cubeEchart)
      myChart.on('click', event => {
        this.$emit('clickbar', event)
      })
      // 绘制图表
      // const option2 = this.deepMerge(option, this.extraOption)
      myChart.setOption(option)

      function mychartsResize() {
        offsetWidth = cubeEchart.parentNode.offsetWidth
        debugger
        cubeEchart.style.width = offsetWidth+"px";
        myChart.resize()
      }
      window.addEventListener('resize', mychartsResize, true);
    },
    deepMerge(obj1, obj2) {
      let key
      for (key in obj2) {
        // 如果target(也就是obj1[key])存在，且是对象的话再去调用deepMerge，否则就是obj1[key]里面没这个对象，需要与obj2[key]合并
        obj1[key] =
          obj1[key] && obj1[key].toString() === '[object Object]'
            ? this.deepMerge(obj1[key], obj2[key])
            : (obj1[key] = obj2[key])
      }
      return obj1
    }
  }
}
</script>

<style scoped></style>
```

#### 1.2、js工具类

```js
import * as echarts from 'echarts'

/**
 * @date 2021-11-29
 * @description 自动生成3D柱状图series函数
 * @param yData 柱状图y轴数据，每条数据第一个数值为图形名称，后面为数值
 * @param colors 柱体颜色
 * @param mode 图形模式：垂直/水平(vertical/horizontal)
 * @return series 生成的柱状图series系列
 */
function generateSeries(yData, colors, mode) {
  const series = []
  const vertical = mode == 'vertical'
  for (let i = 0; i < yData.length; i++) {
    series.push({
      type: 'custom',
      stack: 'custom' + i,
      name: yData[i][0],
      z: vertical ? 10 - i : 10 + i,
      color: colors[i][0],
      renderItem: (params, api) => {
        const location = api.coord([api.value(0), api.value(1)])
        return {
          type: 'group',
          // x: vertical ? 0 : (yData.length - 1 * 20) / yData.length + i * 20 - 10,
          x: vertical ? 0 : i * 20 - 20,
          children: [
            {
              type: 'CubeLeft',
              shape: {
                api,
                xValue: api.value(0),
                yValue: api.value(1),
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: colors[i][0]
              }
            },
            {
              type: 'CubeRight',
              shape: {
                api,
                xValue: api.value(0),
                yValue: api.value(1),
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: colors[i][1]
              }
            },
            {
              type: api.value(1) > 0 ? 'CubeTop' : 'CubeBottom',
              shape: {
                api,
                xValue: api.value(0),
                yValue: api.value(1),
                x: location[0],
                y: location[1],
                xAxisPoint: api.coord([api.value(0), 0])
              },
              style: {
                fill: colors[i][2]
              }
            }
          ]
        }
      },
      data: yData[i].slice(1)
    })
  }
  return series
}

/**
 * @author lijun
 * @date 2021-11-29
 * @description 转换堆叠3D柱状图数据
 * @param data 堆叠柱状图y轴数据
 * @return resultData 转换后的数据
 */
function transferData(data) {
  const resultData = []
  for (let i = 0; i < data.length; i++) {
    const rowData = data[i]
    if (i > 0) {
      for (let j = 0; j < rowData.length; j++) {
        if (j > 0) {
          rowData[j] += data[i - 1][j]
        }
      }
    }
    resultData.push(rowData)
  }
  return resultData
}

/**
 * @author lijun
 * @date 2021-11-29
 * @description echarts注册自定义3D柱形图
 */
function registerShapes() {
  const CubeLeft = echarts.graphic.extendShape({
    //立方体左边形状
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function(ctx, shape) {
      const xAxisPoint = shape.xAxisPoint
      const c0 = [shape.x + 10, shape.y] //右上角
      const c1 = [shape.x - 10, shape.y] //左上角
      const c2 = [xAxisPoint[0] - 10, xAxisPoint[1]] //左下角
      const c3 = [xAxisPoint[0] + 10, xAxisPoint[1]] //右下角
      ctx
        .moveTo(c0[0], c0[1])
        .lineTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .closePath()
    }
  })
  const CubeRight = echarts.graphic.extendShape({
    //立方体右边形状
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function(ctx, shape) {
      const xAxisPoint = shape.xAxisPoint
      const c1 = [shape.x + 10, shape.y] //左上角
      const c2 = [xAxisPoint[0] + 10, xAxisPoint[1]] //左下角
      const c3 = [xAxisPoint[0] + 25, xAxisPoint[1] - 10] //右下角
      const c4 = [shape.x + 25, shape.y - 10] //右上角
      ctx
        .moveTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .lineTo(c4[0], c4[1])
        .closePath()
    }
  })
  const CubeTop = echarts.graphic.extendShape({
    //立方体顶部形状
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function(ctx, shape) {
      const c1 = [shape.x + 10, shape.y] //右下角
      const c2 = [shape.x + 25, shape.y - 10] //右上角
      const c3 = [shape.x + 5, shape.y - 10] //左上角
      const c4 = [shape.x - 10, shape.y] //左下角
      ctx
        .moveTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .lineTo(c4[0], c4[1])
        .closePath()
    }
  })
  const CubeBottom = echarts.graphic.extendShape({
    //立方体底部形状
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function(ctx, shape) {
      const c1 = [shape.xAxisPoint[0] + 10, shape.xAxisPoint[1]] //右下角
      const c2 = [shape.xAxisPoint[0] + 25, shape.xAxisPoint[1] - 10] //右上角
      const c3 = [shape.xAxisPoint[0] + 5, shape.xAxisPoint[1] - 10] //左上角
      const c4 = [shape.xAxisPoint[0] - 10, shape.xAxisPoint[1]] //左下角
      ctx
        .moveTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .lineTo(c4[0], c4[1])
        .closePath()
    }
  })
  const xAxisType = echarts.graphic.extendShape({
    //x轴形状，但是目前用不了API不支持
    shape: {
      x: 0,
      y: 0
    },
    buildPath: function(ctx, shape) {
      const c1 = [shape.x + 10, shape.y] //右下角
      const c2 = [shape.x + 25, shape.y - 10] //右上角
      const c3 = [shape.x + 5, shape.y - 10] //左上角
      const c4 = [shape.x - 10, shape.y] //左下角
      ctx
        .moveTo(c1[0], c1[1])
        .lineTo(c2[0], c2[1])
        .lineTo(c3[0], c3[1])
        .lineTo(c4[0], c4[1])
        .closePath()
    }
  })
  echarts.graphic.registerShape('CubeLeft', CubeLeft)
  echarts.graphic.registerShape('CubeRight', CubeRight)
  echarts.graphic.registerShape('CubeTop', CubeTop)
  echarts.graphic.registerShape('CubeBottom', CubeBottom)
  echarts.graphic.registerShape('xAxisType', xAxisType)
}

let chartUtils = function chartUtil() {
  return {
    generateSeries: generateSeries,
    transferData: transferData,
    registerShapes: registerShapes
  }
}
export default chartUtils()
```

#### 1.3、常量js

```js
const colors = [
  ['rgba(0, 255, 255, 0.8)', 'rgba(0, 115, 206, 0.6)', 'rgba(0, 100, 200, 0.8)'],
  ['rgba(91, 204, 102, 0.9)', 'rgba(5, 138, 28, 0.8)', 'rgba(5, 138, 28, 0.6)'],
  ['rgba(0, 209, 216, 0.9)', 'rgba(6, 83, 151, 0.6)', 'rgba(6, 83, 151, 0.6)']
]

const fontSize = '18'
const color = 'rgba(0,255,255,0.9)'

export default {
  colors,
  fontSize,
  color
}
```

### 2、使用

```vue
<template>
	<div class="grid-content bg-purple">
            <echarts-bar-3d
              :id="'thematicModelBizMonitor11Si'"
              :mode="'horizontal'"
              :yData="yData"
              :coverYAxis="{ name: '/笔', min: 0 }"
              :colors="colors"
              :xData="xData"
              :coverXAxis="coverXAxis"
            />
        </div>
</template>
<script>
import EchartsBar3d from '@/components/3DEcharts/cdp_cube/3D_cube.vue'
import cubeConst from "@/components/3DEcharts/cdp_cube/3dcubeConst"
export default {
  name: 'staticpay',
  components: { EchartsBar3d},
  data() {
    return {
      // 制图数据
      yData: [["数据项1",25,36,47,58,34],["数据项2",15,26,37,48,24]],
      xData: ["测试1","测试2","测试3","测试4","测试5"],
      colors: cubeConst.colors,
      coverXAxis: {
        axisLabel: {
          formatter: function(value) {
            if (value) {
              if (value.trim().length > 5) {
                let res = ''
                // 行（不超过5）
                for (let i = 0; i < 5; i++) {
                  // 每次+5
                  for (let j = 0; j < value.length; j += 5) {
                    // 还有字的时候取字，没有字的时候补空
                    if (i + j < value.length) {
                      res += value.charAt(i + j)
                    } else {
                      res += '    '
                    }
                  }
                  res += '\n'
                }
                return res
              }
              return value.split('').join('\n')
            }
          }
        }
      }
    }
  },
  mounted() {
    console.log(cubeConst.colors)
  },
  methods: {
  }
}
</script>
```

ps:以上组件代码持续优化中，敬请关注
