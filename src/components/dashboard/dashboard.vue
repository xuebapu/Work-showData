<template lang="html">
  <div class="dashboard">
    <div class="flex-container column">
      <div class="item shuju" style="transform: translate(180%, 44%) scale(1)">
        <table cellspacing="3" width="100%" height="100%">
          <tr style="font-size: 30px;color:white">
            <th>闪购销售额</th>
            <th>闪购销售单量</th>
          </tr>
          <tr style="font-size: 50px;color:#10d0cc ">
<!--            :decimals="2" 是否显示小数部分及位数     countTo组件数字变化的动画效果 -->
            <td><countTo :startVal='Oshangou' :endVal='shangou' :duration='2000'></countTo></td>
            <td><countTo :startVal='Oshangou_danliang' :endVal='shangou_danliang' :duration='2000'></countTo></td>
          </tr>
          <tr style="font-size: 30px;color:white">
            <th>秒发销售额</th>
            <th>跨境销售额</th>
          </tr>
          <tr style="font-size: 50px;color:#10d0cc ">
            <td><countTo :startVal='Omiaofa' :endVal='miaofa' :duration='2000'></countTo></td>
            <td><countTo :startVal='Okuajing' :endVal='kuajing' :duration='2000'></countTo></td>
          </tr>
        </table>
      </div>

      <div class="item allNum"style="transform: translate(180%, 0%) scale(1);display: flex">
        <div>注册总人数</div>
        <div style="margin-top: 7%">：</div>
        <div class="chartNum" style="transform: translate(0, 0) scale(1);margin-top: 2%">
          <div class="box-item">
            <li :class="{'number-item': !isNaN(item), 'mark-item': isNaN(item) }"
                v-for="(item,index) in orderNum"
                :key="index">
               <span v-if="!isNaN(item)">
                <i ref="numberItem">0123456789</i>
               </span>
              <span class="comma" v-else>{{item}}</span>
            </li>
            <span style="font-size: 20px;color: white">人</span>
          </div>
        </div>
      </div>
      <div class="item SPPhang" style="transform: translate(3%, 0%) scale(1)">
        <p-hbang :salesX="salesX" :salesY="salesY"></p-hbang>
      </div>
      <div class="item WZPhang" style="transform: translate(180%, 150%) scale(1)">
        <WZPhang :articlesX="articlesX" :articlesY="articlesY"></WZPhang>
      </div>
    </div>
  </div>
</template>

<script>

import PHbang from 'components/PHbang/PHbang'
import WZPhang from 'components/WZPhang/WZPhang'
import axios from "axios";
import countTo from 'vue-count-to';

export default {
  data() {
    return {
      orderNum: ['0', '0', ',', '0', '0', '0', ',', '0', '0', '0'], // 默认注册总人数
      allNum: 0,
      // 统计  用O开头保存旧数据
      Oshangou: 0,
      Oshangou_danliang: 0,
      Omiaofa: 0,
      Okuajing: 0,
      shangou: 0,
      shangou_danliang: 0,
      miaofa: 0,
      kuajing: 0,
      // 销售
      salesData: [],
      salesX: [],
      salesY: [],
      // 文章
      top_articles: [],
      articlesX: [],
      articlesY: [],
      // 连接
      ws: null
    }
  },
  mounted() {
    // l、连接webStroke
    this.getConnection()
  },
  methods: {
    // 设置文字滚动
    setNumberTransform() {
      const numberItems = this.$refs.numberItem // 拿到数字的ref，计算元素数量
      const numberArr = this.orderNum.filter(item => !isNaN(item))
      // 结合CSS 对数字字符进行滚动,显示订单数量
      for (let index = 0; index < numberItems.length; index++) {
        const elem = numberItems[index]
        elem.style.transform = `translate(-50%, -${numberArr[index] * 10}%)`
      }
    },
    // 处理总数
    toOrderNum(num) {
      num = num.toString()
      // 把注册数变成字符串
      if (num.length < 8) {
        num = '0' + num // 如未满八位数，添加"0"补位
        this.toOrderNum(num) // 递归添加"0"补位
      } else if (num.length === 8) {
        // 组测人数中加入逗号
        num = num.slice(0, 2) + ',' + num.slice(2, 5) + ',' + num.slice(5, 8)
        this.orderNum = num.split('') // 将其便变成数据，渲染至滚动数组
        this.setNumberTransform()
      } else {
        // 订单总量数字超过八位显示异常
        this.$message.warning('注册人数已爆表')
      }
    },
    // 连接webStroke
    getConnection() {
      // 判断浏览器是否支持
      this.ws = new WebSocket('ws://52.83.60.235:8881/'); // 后台给的地址
      let that = this;
      let ws = this.ws
      // 连接成功时执行
      ws.onopen = function () {
        console.log("Connection open ...");
      };
      // 连接错误时重新连接
      ws.onerror = function () {
        console.log("err")
        setTimeout(() => {
          ws.onclose();
          that.getConnection();
        }, 1000);
      };
      // 得到服务器传送的数据
      ws.onmessage = (evt) => {
        let x = [];
        let y = [];
        let ax = [];
        let ay = [];
        let data = JSON.parse(evt.data)
        // 购买人数
        this.allNum = (Number)(data.user_count)
        // 统计
        this.Oshangou = this.shangou
        this.shangou = (Number)(data.sales_stats.shangou);
        this.Oshangou_danliang = this.shangou_danliang
        this.shangou_danliang = (Number)(data.sales_stats.shangou_danliang);
        this.Omiaofa = this.miaofa
        this.miaofa = (Number)(data.sales_stats.miaofa);
        this.Okuajing = this.kuajing
        this.kuajing = (Number)(data.sales_stats.kuajing);
        // 销售排行数据
        this.salesData = data.top_sales;
        if (!this.salesData) {
          return
        };
        this.salesData.forEach(item => {
          x.push(item.sales_num);
          y.push(item.name);
        })
        this.salesX = x;
        this.salesY = y;
        // console.log(this.salesX)
        // console.log(this.salesY)
        //  文章排行数据
        this.top_articles = data.top_articles
        if (!this.top_articles) {
          return
        };
        this.top_articles.forEach(item => {
          ax.push(item.value);
          ay.push(item.name);
        })
        this.articlesX = ax;
        this.articlesY = ay;
      }
      // 关闭WebSocket
      ws.onclose = function () {
        console.log("Connection closed.");
      };
    }
  },
  components: {
    PHbang,
    WZPhang,
    countTo
  },
  watch: {
    allNum: {
      handler(newValue, oldValue) {
        this.toOrderNum(newValue)
      },
      deep: true // 深度监听
    }
  }
}
</script>

<style lang="stylus" scoped>
*
  box-sizing: border-box;
.point,.multipleColumn,.columnChart,.line
  height 100%!important
  width 100%!important
  background none!important
.item
    padding: 0px;
    margin: 0px;
    position absolute
    transform scale(0.33)
    text-align: center;
    transition:all 0.8s;
    background rgba(32, 32, 35, 0.5)
.dashboard
    position relative
    width 100%
    height 100%
    margin:0px;
    padding:0px;
    padding-top 1%
    padding-bottom 1%
    background url('../../assets/bg.jpg');
    background-size 100% 100%
.flex-container.column
    position relative
    height: 100%;
    width: 100%;
    overflow: hidden;
    margin:  0 0 0 0;
    box-sizing: content-box;
.shuju
  height 40%
  width: 35%;
  overflow: hidden;
.SPPhang
    height 100%
    width: 60%;
    overflow: hidden;
.WZPhang
  height 40%
  width: 35%;
  overflow: hidden;
.allNum
  height 15%;
  width 35%;
  font-size 20px
  color white
  text-align center;
/*订单总量滚动数字设置*/
.box-item {
  position: relative;
  height: 100px;
  font-size: 54px;
  line-height: 41px;
  text-align: center;
  list-style: none;
  color: #2D7CFF;
  writing-mode: vertical-lr;
  text-orientation: upright;
  /*文字禁止编辑*/
  -moz-user-select: none; /*火狐*/
  -webkit-user-select: none; /*webkit浏览器*/
  -ms-user-select: none; /*IE10*/
  -khtml-user-select: none; /*早期浏览器*/
  user-select: none;
  /* overflow: hidden; */
}
/* 默认逗号设置 */
.mark-item {
  width: 10px;
  height: 100px;
  margin-right: 5px;
  line-height: 10px;
  font-size: 48px;
  position: relative;
  & > span {
    position: absolute;
    width: 100%;
    bottom: 0;
    writing-mode: vertical-rl;
    text-orientation: upright;
  }
}
/*滚动数字设置*/
.number-item {
  width: 50px;
  height: 75px;
  background: #ccc;
  list-style: none;
  margin-right: 5px;
  background:rgba(250,250,250,1);
  border-radius:4px;
  border:1px solid rgba(221,221,221,1);
  & > span {
    position: relative;
    display: inline-block;
    margin-right: 10px;
    width: 100%;
    height: 100%;
    writing-mode: vertical-rl;
    text-orientation: upright;
    overflow: hidden;
    & > i {
      font-style: normal;
      position: absolute;
      top: 11px;
      left: 50%;
      transform: translate(-50%,0);
      transition: transform 2s ease-in-out;
      letter-spacing: 10px;
    }
  }
}
.number-item:last-child {
  margin-right: 0;
}

</style>
