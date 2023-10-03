<template>
  <el-container>
    <el-aside style="background:#324057" width="200px">
      <div style="height: 60px; width: 100%;"></div>
      <el-menu background-color="#324057" text-color="#fff" default-active="2" class="el-menu-vertical-demo">
        <myMenus :routerList="baseMenu" @updataPages="updataPages"></myMenus>
      </el-menu>
    </el-aside>
    <el-container>
      <el-header>
        <el-page-header style="margin-top: 18px;" @back="goBack">
          <template #content>
            <span class="text-large font-600 mr-3"> {{ nowMenuItem.name }} </span>
          </template>
          <template #extra>
            <div class="flex items-center">
              <el-dropdown @command="selectVersion">
                <el-button type="primary" plain>
                  {{ nowVersion.version }}<el-icon class="el-icon--right"><arrow-down /></el-icon>
                </el-button>
                <template #dropdown>
                  <el-dropdown-menu>
                    <el-dropdown-item v-for="item in versions" :key="item.index" :command="item.index">{{ item.version
                    }}</el-dropdown-item>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
            </div>
          </template>
        </el-page-header>
      </el-header>
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
import { ElMessage } from 'element-plus';

var echartMainGlobal;
export default {
  data() {
    return {
      baseMenu: [],
      nowMenuItem: {},
      Pages: [],
      Page: {},
      echartMainHeight: '600px',
      cards_0: [],
      cards_1: [],
      beforeMenuItem: [],
      versions: [],
      nowVersion: {},
      echartMainTotalValue: 1
    }
  },
  components: {
    myMenus,
    myCard
  },
  created: function () {
  },
  mounted: function () {
    axios.get('./data/contents.json').then((res) => {
      console.log('res.data = ', res.data)
      this.baseMenu = res.data
      let nowMenuItem = this.baseMenu[0]
      while (nowMenuItem.children) {
        nowMenuItem = nowMenuItem.children[0]
      }
      this.nowMenuItem = nowMenuItem
      echartMainGlobal = echarts.init(document.getElementById('echart_main'));
      this.updataPages(this.nowMenuItem)
    })
  },
  methods: {
    updataPages(item) {
      if (this.beforeMenuItem.length >= 10) {
        this.beforeMenuItem.pop()
      }
      this.beforeMenuItem.push(this.nowMenuItem)
      this.nowMenuItem = item
      console.log("path is ", item.path)
      axios.get(item.path + '/versions.json').then((res) => {
        console.log('res.data = ', res.data)
        this.Pages = res.data
        this.showVersions(res.data)
        this.showPage(0)
      })
    },
    showVersions(pages) {
      this.versions = []
      for (let idx in pages) {
        let page = pages[idx]
        this.versions.push(
          {
            index: idx,
            version: page.version
          }
        )
      }
      console.log("version", this.versions)
    },
    showPage(index) {
      this.nowVersion = this.versions[index]
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
      this.echartMainTotalValue = val.totalValue
      for (let index in val.data) {
        let data = val.data[index]
        let echartMainData = {
          name: data.name ? data.name : 'no_name',
          type: 'bar',
          stack: 'total',
          label: {
            show: true,
            formatter: `${data.value}(${(data.value / val.totalValue * 100).toFixed(2)}%)`
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
    },
    selectVersion(versionIdx) {
      this.showPage(versionIdx)
    },
    goBack() {
      if (this.beforeMenuItem.length == 0) {
        return
      }
      this.nowMenuItem = this.beforeMenuItem.pop()
      axios.get(this.nowMenuItem.path + '/versions.json').then((res) => {
        this.Pages = res.data
        this.showVersions(res.data)
        this.showPage(0)
      })
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
