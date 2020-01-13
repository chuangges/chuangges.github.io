---
title: Web 图表插件
tags:
  - 插件
  - 项目开发
categories: 项目开发
top: false
keywords:
  - 插件
date: 2019-03-22 22:06:14
description: Echarts
---

# 一、ECharts
> 一个免费的、功能强大的、可视化的一个库，可以非常简单的往软件产品中添加直观的、动态的和高度可定制化的图表。它是一个全新的基于矢量图形库 zrender 的使用纯 JavaScript 打造完成的 canvas 库。

## 引用方式

### Vue
> 安装：`cnpm install echarts -S`
  
  
  ```js
  // 全局引用
  // main.js
  import echarts from 'echarts'
  Vue.prototype.$echarts = echarts
  // 组件使用
  <div ref="chart" style="width:500px;height:500px"></div>
  export default {
    methods: {
  　　initCharts () {
    　　let myChart = this.$echarts.init(this.$refs.chart);
    　　// 绘制图表
    　　myChart.setOption({ })
  　　}
  　  },
  　  mounted () {
  　　  this.initCharts();
  　  }
  }

  // 按需引用：组件中直接使用
  import echarts from 'echarts';
  const echarts = require('echarts');
  export default {
    methods: {
      initCharts () {
        let myChart = echarts.init(this.$refs.chart);
        myChart.setOption({ })
      }
    },
    mounted () {
  　　 this.initCharts();
  　}
  }
  ```


### react

  1. 安装
    ```js
    npm install --save echarts-for-react
    npm install echarts --save  // 如果有报错找不到 echarts 模块则安装
    ```
  2. 引入模块和组件
    ```js
    // 全局引用
    import echarts from 'echarts' 
    import echarts from 'echarts/lib/echarts'
    <ReactEcharts option={this.getOption()} />

    // 按需引用
    import React from 'react';
    import {Card} from 'antd';
    import echartTheme from './../themeLight'
    import echarts from 'echarts/lib/echarts'
    //导入折线图
    import 'echarts/lib/chart/line';  //折线图是line,饼图改为pie,柱形图改为bar
    import 'echarts/lib/component/tooltip';
    import 'echarts/lib/component/title';
    import 'echarts/lib/component/legend';
    import 'echarts/lib/component/markPoint';
    import ReactEcharts from 'echarts-for-react';
    export default class Line extends React.Component{
      componentWillMount(){
        //主题的设置要在willmounted中设置
        echarts.registerTheme('Imooc',echartTheme);
      }
      getOption =()=> {
        let option = {
          title:{
            text:'用户骑行订单',
            x:'center'
          },
          tooltip:{
            trigger:'axis',
          },
          xAxis:{
            data:['周一','周二','周三','周四','周五','周六','周日']
          },
          yAxis:{
            type:'value'
          },
          series:[
            {
              name:'OFO订单量',
              type:'line',   //这块要定义type类型，柱形图是bar,饼图是pie
              data:[1000,2000,1500,3000,2000,1200,800]
            }
          ]
        }
        return option
      }
      render(){
        return(
          <div>
            <Card title="折线图表之一">
                <ReactEcharts option={this.getOption()} theme="Imooc"  style={{height:'400px'}}/>
            </Card>

          </div>
        )
      }
    }
    ```
  3. 添加 option 参数



## 配置选项
> 图表选项 option 包含了图表实例任何可配置的选项：`公共选项、组件选项、数据选项`

  * https://www.jianshu.com/p/62b95115f522
  * https://my.oschina.net/u/3363053/blog/900173


## 应用实例

  ```js
  function initEachart(list) {
    let callNumList = list || [];
    let mainEcharts = echarts.init(this.$refs.echart);
    mainEcharts.setOption({
      xAxis: {
        type: "category",
        boundaryGap: false,
        data: ["0点", "1点", "2点", "3点", "4点", "5点", "6点", "7点", "8点",
              "9点", "10点", "11点", "12点", "13点", "14点", "15点", "16点",
              "17点", "18点", "19点", "20点", "21点", "22点", "23点"
        ],
        splitLine: {
          show: true,
          lineStyle: {
            color: "#F8F8F8"
          }
        },
        axisTick: { show: false },
        axisLine: {
          lineStyle: { color: "#475669" }
        },
        axisLabel: {
          interval: 0,
          formatter: function(value, index) {
            if (index == 0) {
              return "\t\t\t\t\t" + value;
            } else if (index == 23) {
              return value + "\t\t\t\t\t";
            } else {
              return value;
            }
          }
        }
      },
      color: ["#3B8EFF"],
      yAxis: {
        min: 0,
        type: "value",
        splitLine: {
          show: true,
          lineStyle: { color: "#F8F8F8" }
        },
        axisTick: { show: false },
        axisLine: {
          lineStyle: { color: "#475669" }
        },
        axisLabel: {
          formatter: function(value, index) {
            if (index == 0) {
              return value + "\n";
            } else if (index == 3) {
              return "\n" + value;
            } else {
              return value;
            }
          }
        }
      },
      series: [
        {
          data: callNumList,
          type: "line",
          smooth: true,
          areaStyle: {
            normal: { opacity: "0.2" }
          },
          itemStyle: {
            normal: {
              label: {
                show: true,
                position: [6, -14],
                textStyle: {
                  color: "#475669",
                  fontSize: "14px"
                }
              }
            }
          }
        }
      ],
      grid: {
        left: "40",
        right: "30",
        top: "20",
        bottom: "30"
      }
    })
  }
  ```





























