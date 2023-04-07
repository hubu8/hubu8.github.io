# Echarts之3D柱之圆形柱状图封装


<!--more-->

### 0、最终实现效果

### 1、组件代码

![](https://raw.githubusercontent.com/hubu8/hubu8.github.io/static_picture/hugo/image-20230407150253966.png)

#### 1.1、Vue代码

```vue
<template>
  <div :id="id" class="cylinder-3d"></div>
</template>

<script>
import { get3DOptions } from "./options.js";
import * as echarts from 'echarts'
export default {
  name: "outputMea",
  props:{
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
  },
  mounted() {
    this.init();
  },
  methods:{
    init() {
      let echartsDom = document.getElementById(this.id);
      //日总产量
      let output_3DBarCharts = echarts.init(echartsDom);
      let option = get3DOptions({xData: this.xData,yData: this.yData});
      output_3DBarCharts.setOption(option);
      this.fetchSize(echartsDom)
      var that = this
      window.addEventListener("resize", function () {
        that.fetchSize(echartsDom)
        output_3DBarCharts.resize();
      },true);
    },
    fetchSize(echartsDom){
      let offsetWidth = echartsDom.parentNode.offsetWidth
      let offsetHeight = echartsDom.parentNode.offsetHeight
      echartsDom.style.width = offsetWidth+"px";
      echartsDom.style.height = offsetHeight+"px";
      debugger
    }
  }
}
</script>

<style scoped lang="scss">
.cylinder-3d{
  width: 100%;
  height: 100%;
  margin: 0 20px;
  background: no-repeat 46px 93.5%;
  background-size: calc(100% - 60px) auto;
}
//   #output_3DBarCharts{
//     width: calc(100% - 40px);
//     height: calc(100% - 98px);
//     margin: 0 20px;
//     background: url("../../assets/bigData/3dBarBg.png") no-repeat 46px 93.5%;
//     background-size: calc(100% - 60px) auto;
//   }
</style>
```

#### 1.2、js工具类

```js
import * as echarts from 'echarts'

/*3D柱形图*/
const get3DOptions = (op = {}) => {
  var options = {
    grid: {
      left: 60,
      right: 20,
      top: 50,
      bottom: 40
    },
    legend: {
      show: true,
      icon: 'circle',
      orient: 'horizontal',
      top: '90.5%',
      right: 'center',
      itemWidth: 16.5,
      itemHeight: 6,
      textStyle: {
        color: '#C9C8CD',
        fontSize: 14
      }
    },
    xAxis: [{
      data: op.xData || [],
      axisLabel: {
        show: true,
        textStyle: {
          color: '#aaaaaa',
          fontSize: 12
        },
        margin: 30, //刻度标签与轴线之间的距离。
      },
      axisLine: {
        show: false //不显示x轴
      },
      axisTick: {
        show: false //不显示刻度
      },
      boundaryGap: true,
      splitLine: {
        show: false,
        width: 1,
        lineStyle: {
          type: "solid",
          color: "#03202E"
        }
      }
    }],
    yAxis: [{
      show: true,
      axisLabel: {
        interval: 'auto',
        show: true,
        textStyle: {
          fontSize: 14,
          color: '#fff',
        },
      },
      splitLine: {
        show: false,
        lineStyle: {
          color: 'rgba(49,176,255,0.05)',
        },
      },
      axisTick: {
        show: false,
      },
      axisLine: {
        show: true,
        lineStyle: {
          color: 'rgba(49,176,255,0.5)',
        },
      },
    }],
    series: [
      {//柱底圆片
        name: "",
        type: "pictorialBar",
        symbolSize: [20, 8],//调整截面形状
        symbolOffset: [0, 4],
        z: 12,
        itemStyle: {
          normal: {
            color: '#183E96',
          }
        },
        data: op.yData || []
      },
      //柱体
      {
        name: '',
        type: 'bar',
        barWidth: 20,
        barGap: '0%',
        itemStyle: {
          color: () => {
            return new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              { offset: 0, color: '#01AEF6'},
              { offset: 1, color: 'rgba(16,36,95,0.6)' },
            ])
          },
          opacity:1
        },
        data: op.yData || []
      },
      //柱顶圆片
      {
        name: "",
        type: "pictorialBar",
        symbolSize: [20, 8],//调整截面形状
        symbolOffset: [0, -5],
        z: 12,
        symbolPosition: "end",
        label: {
          show: true,
          position: 'top',
          textStyle: {
            color: '#fff'
          }
        },
        itemStyle: {
          normal: {
            color: '#17E1FF',
          }
        },
        data: op.yData || []
      }
    ]
  }
  return options;
};

export { get3DOptions }
```

### 2、如何使用

```vue
<template>
<div class="grid-content bg-purple">
          <echarts-cylinder3d
            :id="'thematicModelBizMonitor13Si'"
            :yData="yData1"
            :xData="xData1"
          />
        </div>
</template>
<script>
import EchartsCylinder3d from '@/components/3DEcharts/cylinder/3D_cylinder.vue'
export default {
  name: 'staticpay',
  components: { LiquidfillEchart},
  data() {
    return {
      yData1: [25,36,47,58,34],
      xData1: ["测试1","测试2","测试3","测试4","测试5"],
  },
  mounted() {
    console.log(cubeConst.colors)
  },
  methods: {
  }
}
</script>
```


