<template>
  <div class="ChartProjectVulnerabilities">
    <div id="ChartProjectVulnerabilities-content"></div>
  </div>
</template>


<script>
  import common from "../../shared/common"
  export default {
    data() {
      return {

      }
    },
    methods: {
      render (resData) {
        let {xData, obj} = this.processingData(resData) // X轴/y轴数据
        const echarts = require("echarts");  //换这个方法引入echarts
        const myEcharts = echarts.init(document.getElementById('ChartProjectVulnerabilities-content'));
        let colorData = [
          ['#e9cdb3', '#e9cdb3'], // 严重 1
          ['#777777', '#777777'], // 高危 2
        ] // 色块
        let testData = [];
        let seriesArr = [];
        for (let item in obj) {
          testData.push(item)
          seriesArr.push(obj[item])
        }
        let arr = []
        let arrIndex = []
        obj['全部的'].map((res, index) => {
          arrIndex.push(index + 1)
          let arrs = []
          seriesArr.map(i => {
            arrs.push(i[index])
          })
          console.log(arrs)
          arr.push(arrs.reduce((a, b) => a+b))
        })
        let objs = JSON.parse(JSON.stringify(seriesArr))
        objs.map((res, index) => {
          if (index > 0) {
            res.map((item, iIndex) => {
              objs[index][iIndex] = item + objs[index -1][iIndex]
            })
          }
        })
        // 数据
        let sumValue = arr;
        let option = {
          backgroundColor: "#1a2439", //背景色
          tooltip: {
            show: true,
          },
          textStyle: {
            color: "#C9C9C9",
          },
          legend: {
            type: "scroll",
            selectedMode: false, //图例点击失效
            top: "0%",
            left:"35%",
            textStyle: {
              color: "#fff",
              fontSize: 14,
            },
          },
          // 图表位置
          grid: {
            containLabel: true,
            left: "5%",
            top: "15%",
            bottom: "10%",
            right: "5%",
          },
          xAxis: {
            type: "category",
            data: xData,
            // x轴底部文字
            axisLabel: {
              margin: 20, //刻度标签与轴线之间的距离。
              textStyle: {
                fontFamily: "Microsoft YaHei",
                color: "#e9cdb3",
              },
              fontSize: 16,
              fontStyle: "bold"
            },
          },
          yAxis: {
            type: "value",
            axisLine: {
              show: false,
              lineStyle: {
                color: "#B5B5B5",
              },
            },
            splitLine: {
              show: true,
              lineStyle: {
                color: '#466ABA'
              }

            },
            axisLabel: {
              textStyle: {
                fontFamily: "Microsoft YaHei",
                color: "#e9cdb3",
              },
              fontSize: 16,
            },
          },
          series: seriesData(seriesArr)
        };
        console.log(option)
        myEcharts.setOption(option);

        function seriesData(data) {
          let barData = [];
          let pictorialBarData = [];
          data.map((item, index) => {
            barData.push({
              "name": testData[index],
              "type": "bar",
              data: item,
              stack: "zs",
              type: "bar",
              barMaxWidth: "auto",
              barWidth: 50,
              itemStyle: {
                color: {
                  x: 0,
                  y: 0,
                  x2: 0,
                  y2: 1,
                  type: "linear",
                  global: false,
                  colorStops: [{
                    offset: 0,
                    color: colorData[index][0],
                  },
                    {
                      offset: 1,
                      color: colorData[index][1],
                    },
                  ],
                },
              },
              label: {
                show: true,
                offset: [0, 0]
              },
              labelLine: {
                show: true,
              }
            });
            pictorialBarData.push({
              // data: objs[index],
              type: "pictorialBar",
              barMaxWidth: "20",
              symbolPosition: "end",
              symbol: "diamond",
              symbolOffset: [0, "-50%"],
              symbolSize: [50, 20],
              zlevel: 2,
            })
          })
          let btArr = [];
          let arr = [...barData, ...btArr, ...pictorialBarData];
          return arr;
        }
      },
      processingData (data) {
        data = data.slice(-5)
        console.log(data, '@data')
        let xData = []
        let obj = {
          '全部的': [],
          '脆弱的': [],
        }
        data.forEach((item, index) => {
          if (index === data.length - 1) {
            xData.push(common.formatTimestamp(item.lastOccurrence));
          } else {
            xData.push(common.formatTimestamp(item.firstOccurrence));
          }          obj['全部的'].push(item.projects);
          obj['脆弱的'].push(item.vulnerableProjects);
        })
        return {xData, obj}
      }
    },
    mounted() {
    }
  }
</script>
<style>
  #ChartProjectVulnerabilities-content {
    width: 100%;
    height: 100%;
  }
</style>
