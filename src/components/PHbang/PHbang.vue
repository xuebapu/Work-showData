<template>
  <div class="PHbang">
    <div class="name">{{name}}</div>
    <div class="main"></div>
  </div>

</template>

<script>

import echarts from 'echarts'

export default {
  props: {// 接收父组件传值
    salesX: {
      type: Array,
      default() {
        return []
      }
    },
    salesY: {
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
      name: '酒品销售排行榜'
    }
  },
  created() {
    this.$nextTick(() => {
      this.getMyCharts()
    })
  },
  methods: {
    i_init(options) {
      this.myChart = echarts.init(document.querySelector('.PHbang .main'))
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
        tooltip: {
          trigger: "item",
          formatter: (p) => {
            if (p.seriesName === "total") {
              return "";
            }
            return `${p.name}<br/>销售量: ${p.value}`;
          }
        },
        legend: {
          show: true
        },
        grid: {
          left: 40,
          top: 20,
          right: 70
        },
        xAxis: {
          type: "value",
          name: "销售量/瓶",
          nameLocation: "end",
          nameTextStyle: {
            fontSize: 16,
            color: "#e2e4e8",
            padding: [60, 0, 0, -65]
          },
          splitLine: { // 是否显示分割线
            show: false
          },
          axisLabel: {
            show: true,
            color: "#e2e4e8"
          },
          axisTick: {
            show: false
          },
          axisLine: {
            show: true,
            lineStyle: {
              color: "#e2e4e8"
            }
          }
        },
        yAxis: [
          {
            type: "category",
            inverse: true,
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            axisPointer: {
              label: {
                show: false,
                margin: 30
              }
            },
            data: this.salesY,
            axisLabel: {
              margin: 40,
              fontSize: 30,
              align: "left",
              color: "#333",
              // 配置序号背景
              rich: {
                a1: {
                  color: "#fff",
                  backgroundColor: {
                    image: "../../static/img/demo.jpg"
                  },
                  width: 30,
                  height: 30,
                  align: "center",
                  borderRadius: 3
                },
                a2: {
                  color: "#fff",
                  backgroundColor: {
                    image: "../../static/img/demo.jpg"
                  },
                  width: 30,
                  height: 30,
                  align: "center",
                  borderRadius: 3
                },
                a3: {
                  color: "#fff",
                  backgroundColor: {
                    image: "../../static/img/demo.jpg"
                  },
                  width: 30,
                  height: 30,
                  align: "center",
                  borderRadius: 3
                },
                b: {
                  color: "#fff",
                  backgroundColor: "#9ec6fe",
                  width: 30,
                  height: 30,
                  lineHeight: 30,
                  align: "center",
                  verticalAlign: "middle",
                  borderRadius: 5
                }
              },
              formatter: function (params, index) {
                var leftIndex = index + 1;
                if (leftIndex < 4) {
                  return ["{a" + leftIndex + "|" + leftIndex + "}" + "  "].join(
                    "\n"
                  );
                } else {
                  return ["{b|" + leftIndex + "}" + "  "].join("\n");
                }
              }
            }
          }
        ],
        series: [
          // 用两个数据重合 一个把名字显示到排行榜中并设置z（-index）=999置于顶层  一个显示数据到排行榜右边
          {
            name: "value",
            type: "bar",
            barWidth: 35,
            barGap: '-100%',
            z: 9999,
            legendHoverLink: false,
            data: this.salesX,
            itemStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [{
                  offset: 0,
                  color: '#0590eb' // 0% 处的颜色
                }, {
                  offset: 1,
                  color: '#08e3f1' // 100% 处的颜色
                }], false),
                formatter: function (a) {
                  return `${a.name}`;
                },
                barBorderRadius: [0, 15, 15, 0],
                shadowColor: 'rgba(0,0,0,0.1)',
                shadowBlur: 3,
                shadowOffsetY: 3
              }
            },
            // 配置柱子上方类目轴名字
            label: {
              normal: {
                show: true,
                position: [2, 9],
                textStyle: {
                  fontSize: 20,
                  color: '#f5f0f0'
                },
                formatter: function (a) {
                  return `${a.name}`;
                }
              }
            }
          },
          {
            name: "value",
            type: "bar",
            barWidth: 35,
            barGap: '-100%',
            legendHoverLink: false,
            data: this.salesX,
            itemStyle: {
              normal: {
                color: new echarts.graphic.LinearGradient(0, 0, 1, 0, [{
                  offset: 0,
                  color: '#0590eb' // 0% 处的颜色
                }, {
                  offset: 1,
                  color: '#08e3f1' // 100% 处的颜色
                }], false),
                formatter: function (a) {
                  return `${a.name}`;
                },
                barBorderRadius: [0, 15, 15, 0],
                shadowColor: 'rgba(0,0,0,0.1)',
                shadowBlur: 3,
                shadowOffsetY: 3
              }
            },
            // 右边显示的数据
            label: {
              normal: {
                show: true,
                textStyle: {
                  color: '#f8f5f5',
                  fontSize: 17
                },
                position: 'right',
                formatter: function(b) {
                  return `${b.value}`;
                }
              }
            }
          }
        ],
        animationDuration: 2000,
        animationEasing: "cubicInOut",
        animationDurationUpdate: 2000,
        animationEasingUpdate: "cubicInOut"
      };
      this.i_init(paihangOption)
    }
  },
  components: {
  },
  watch: { // 监听数据改动从新渲染
    salesX: {
      handler(newValue, oldValue) {
        this.getMyCharts()
      },
      deep: true // 深度监听
    }
  }
}
</script>
<style lang="stylus" scoped>
.PHbang
  width 100%
  height 100%
  background-size 100% 100%
  color white
  .name
    margin-top 2%
    width 100%
    height 5%
    color #d0c2ac
    font-size 25px
    line-height 5%
    text-align bottom
  .main
    width 100%
    height 98%
    margin-top -3%
    color white


</style>
