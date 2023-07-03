<template>
  <div class="ChartPortfolioVulnerabilities">
    <div id="ChartPortfolioVulnerabilities-content"></div>
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
        const myEcharts = echarts.init(document.getElementById('ChartPortfolioVulnerabilities-content'));
        let colorData = [
          ['#f86c6b', '#f86c6b'], // 严重 1
          ['#fd8c00', '#fd8c00'], // 高危 2
          ['#ffc107', '#ffc107'], // 中危 3
          ['#4dbd74', '#4dbd74'], // 低危 4
          ['#777777', '#777777'] // 未分配  5
        ] // 色块
        let testData = [];
        let seriesArr = [];
        for (let item in obj) {
          testData.push(item)
          seriesArr.push(obj[item])
        }
        let arr = []
        let arrIndex = []
        obj['高危'].map((res, index) => {
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
            top: "5%",
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
        data = data.slice(-10)
        let xData = []
        let obj = {
          '严重': [],
          '高危': [],
          '中危': [],
          '低危': [],
          '未分配': []
        }
        data.forEach((item, index) => {
          if (index === data.length - 1) {
            xData.push(common.formatTimestamp(item.lastOccurrence));
          } else {
            xData.push(common.formatTimestamp(item.firstOccurrence));
          }
          obj['严重'].push(item.critical);
          obj['高危'].push(item.high);
          obj['中危'].push(item.medium);
          obj['低危'].push(item.low);
          obj['未分配'].push(item.unassigned);
        })
        return {xData, obj}
      }
    },
    mounted() {
    }
  }
</script>
<style>
  #ChartPortfolioVulnerabilities-content {
    width: calc(100% - 2.8rem);
    height: 100%;
  }
</style>
