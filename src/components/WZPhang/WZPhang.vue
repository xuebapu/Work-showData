<template>
  <div class="WZPhang">
    <div class="name">{{name}}</div>
    <div class="main"></div>
  </div>
</template>

<script>

import echarts from 'echarts'
import axios from "axios";

export default {
  props: {
    articlesX: {
      type: Array,
      default() {
        return []
      }
    },
    articlesY: {
      type: Array,
      default() {
        return []
      }
    }
  },
  data() {
    return {
      legendArr: [],
      myChart: {},
      name: '文章TOP排行榜'
    }
  },
  created() {
    this.$nextTick(() => {
      this.getMyCharts()
    })
  },
  methods: {
    i_init(options) {
      this.myChart = echarts.init(document.querySelector('.WZPhang .main'))
      this.myChart.setOption(options)
      this.legendArr = options.series
      this.legendArr.forEach((data) => {
        data.selected = true;
      })
      this.$root.charts.push(this.myChart)
      window.addEventListener('resize', function() {
        this.myChart.resize()
      }.bind(this))
    },
    getMyCharts() {
      let paihangOption = {
        // color: ['#3398DB'],
        tooltip: {
          trigger: 'axis',
          backgroundColor: 'rgba(255,255,255,0.8)',
          extraCssText: 'box-shadow: 0 0 8px rgba(0, 0, 0, 0.3);',
          textStyle: {
            color: '#6a717b'
          }
        },
        grid: {
          top: '3%',
          left: '0%',
          right: '4%',
          bottom: '1%',
          containLabel: true
        },
        yAxis: [{
          type: 'category',
          data: this.articlesY,
          inverse: true,
          axisTick: {
            alignWithLabel: true
          },
          axisLabel: {
            formatter: function(params) {
              var newParamsName = "";// 最终拼接成的字符串
              var paramsNameNumber = params.length;// 实际标签的个数
              var provideNumber = 5;// 每行能显示的字的个数
              var rowNumber = Math.ceil(paramsNameNumber / provideNumber);// 换行的话，需要显示几行，向上取整
              /**
               * 判断标签的个数是否大于规定的个数， 如果大于，则进行换行处理 如果不大于，即等于或小于，就返回原标签
               */
              // 条件等同于rowNumber>1
              if (paramsNameNumber > provideNumber) {
                /** 循环每一行,p表示行 */
                for (var p = 0; p < rowNumber; p++) {
                  var tempStr = "";// 表示每一次截取的字符串
                  var start = p * provideNumber;// 开始截取的位置
                  var end = start + provideNumber;// 结束截取的位置
                  // 此处特殊处理最后一行的索引值
                  if (p === rowNumber - 1) {
                    // 最后一次不换行
                    tempStr = params.substring(start, paramsNameNumber);
                  } else {
                    // 每一次拼接字符串并换行
                    tempStr = params.substring(start, end) + "\n";
                  }
                  newParamsName += tempStr;// 最终拼成的字符串
                }
              } else {
                // 将旧标签的值赋给新标签
                newParamsName = params;
              }
              // 将最终的字符串返回
              return newParamsName
            },
            margin: 10,
            textStyle: {
              width: 10,
              fontSize: 16,
              color: 'rgba(255, 255, 255, 0.69)'
            }
          },
          axisLine: {
            lineStyle: {
              color: 'rgba(255, 255, 255, 0.69)'
            }
          }
        }],
        xAxis: [{
          type: 'value',
          axisLabel: {
            margin: 5,
            textStyle: {
              fontSize: 12,
              color: 'rgba(255, 255, 255, 0.69)'
            }
          },
          axisLine: {
            lineStyle: {
              color: 'rgba(255, 255, 255, 0.69)'
            }
          },
          splitLine: {
            lineStyle: {
              color: 'rgba(255, 255, 255, 0.69)'
            }
          }
        }],
        // backgroundColor: '#192469',
        series: [{
          name: '',
          type: 'bar',
          barWidth: 15,
          data: this.articlesX,
          label: {
            normal: {
              show: true,
              position: 'insideRight',
              textStyle: {
                color: 'white' // color of value
              }
            }
          },
          itemStyle: {
            normal: {
              color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [{
                offset: 0,
                color: '#0590eb' // 0% 处的颜色
              }, {
                offset: 1,
                color: '#08e3f1' // 100% 处的颜色
              }], false),
              barBorderRadius: [0, 15, 15, 0],
              shadowColor: 'rgba(0,0,0,0.1)',
              shadowBlur: 3,
              shadowOffsetY: 3
            }
          }
        }]
      }
      this.i_init(paihangOption)
    }
  },
  components: {
  },
  watch: {
    articlesY: {
      handler(newValue, oldValue) {
        this.getMyCharts()
      },
      deep: true // 深度监听
    }
  }
}

</script>
<style lang="stylus" scoped>
.WZPhang
  width 100%
  height 100%
  background-size 100% 100%
  color white
  .name
    margin-top 2.5%
    width 100%
    height 5%
    color #d0c2ac
    font-size 15px
    line-height 5%
    text-align bottom
  .main
    width 95%
    height 95%
    margin-top -3%


</style>
