<template>
  <el-container>
    <el-aside style="background:#324057" width="200px">
      <div style="height: 60px; width: 100%;"></div>
      <el-menu background-color="#324057" text-color="#fff" default-active="2" class="el-menu-vertical-demo"
        @open="handleOpen" @close="handleClose">
        <myMenus :routerList="baseMenu" @updataPages="updataPages"></myMenus>
      </el-menu>
    </el-aside>
    <el-container>
      <el-header>Header</el-header>
      <el-main style="background:#e5e9f2">
        <el-row :gutter="20">
          <el-col :span="12">
            <el-card class="box-card">
              <div ref="echart_main" id="echart_main" :style="{ height: echartMainHeight }" />
            </el-card>
          </el-col>
          <el-col :span="12">
            <el-scrollbar :height="'' + (parseInt(echartMainHeight) + 40) + 'px'">
              <el-row :gutter="20" style="width: 100%">
                <el-col :span="12" id="card_list_0">
                  <el-row :gutter="20">
                    <myCard v-for="card in cards_0" :key=card.index :cardData="card">
                    </myCard>
                  </el-row>
                </el-col>
                <el-col :span="12" id="card_list_1">
                  <el-row :gutter="20">
                    <myCard v-for="card in cards_1" :key=card.index :cardData="card">
                    </myCard>
                  </el-row>
                </el-col>
              </el-row>
            </el-scrollbar>
          </el-col>
        </el-row>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import myMenus from './components/my_menus.vue'
import myCard from './components/my_card.vue'
import axios from 'axios'  // 安装axios后引入
import * as echarts from 'echarts'
import { createApp, h } from 'vue'

var echartMainGlobal;
export default {
  data() {
    return {
      baseMenu: [],
      Pages: [],
      Page: {},
      echartMainHeight: '600px',
      cards_0: [],
      cards_1: []
    }
  },
  components: {
    myMenus,
    myCard
  },
  created: function () {
    axios.get('./data/contents.json').then((res) => {
      console.log('res.data = ', res.data)
      this.baseMenu = res.data
    })
  },
  mounted: function () {
    echartMainGlobal = echarts.init(document.getElementById('echart_main'));
    echartMainGlobal.setOption({
      xAxis: {
        type: 'category',
        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
      },
      yAxis: {
        type: 'value'
      },
      series: [{
        data: [820, 932, 901, 934, 1290, 1330, 1320],
        type: 'line'
      }]
    })
  },
  methods: {
    updataPages(item) {
      console.log("path is ", item.path)
      axios.get(item.path + '/versions.json').then((res) => {
        console.log('res.data = ', res.data)
        this.Pages = res.data
        this.showPage(0)
      })
    },
    showPage(index) {
      this.Page = this.Pages[index]
      axios.get(this.Page.path).then((res) => {
        console.log('res.data = ', res.data)
        this.showEchartMain(res.data.echart_main)
        this.showCards(res.data.cards)
      })
    },
    showEchartMain(val) {
      let echartMainLayers = val.layers
      let echartMainDataList = []
      for (let index in val.data) {
        let data = val.data[index]
        let echartMainData = {
          name: data.name ? data.name : 'no_name',
          type: 'bar',
          stack: 'total',
          label: {
            show: true
          },
          emphasis: {
            focus: 'series'
          },
          data: [[data.value, data.layer]]
        }
        echartMainDataList.push(echartMainData)
      }
      let echartMainOption = {
        title: {
          text: val.title,
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            // Use axis to trigger tooltip
            type: 'line' // 'shadow' as default; can also be 'line' or 'shadow'
          }
        },
        legend: {
          type: 'scroll',
          right: 0
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: {
          type: 'value'
        },
        yAxis: {
          type: 'category',
          data: echartMainLayers
        },
        series: echartMainDataList
      }
      console.log("optiomn:", echartMainOption)

      this.echartMainHeight = val.height
      echartMainGlobal.setOption(echartMainOption)
      echartMainGlobal.resize({ height: val.height })

    },
    showCards(val) {
      this.cards_0 = []
      this.cards_1 = []
      for (let idx in val) {
        this.showCard(val[idx])
      }
    },
    showCard(val) {
      let cards = this.cards_0.length > this.cards_1.length ? this.cards_1 : this.cards_0;
      val.index = cards.length
      cards.push(val)
      console.log(cards, this.cards_0, this.cards_1)
    }
  }
}
</script>

<style scoped>
html,
body,
#app,
.el-container {
  height: 100%;
}
</style>
