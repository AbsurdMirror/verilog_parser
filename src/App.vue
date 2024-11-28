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
        </el-page-header>
      </el-header>
      <el-main style="background:#e5e9f2">
        <el-select v-model="value" style="width: 150px">
          <el-option v-for="item in filelist" :label="item.instance_name" :value="item.instance_name"></el-option>
          <el-option label="选项一" value="option1"></el-option>
          <el-option label="选项二" value="option2"></el-option>
          <el-option label="选项三" value="option3"></el-option>
        </el-select>
        <p>{{ code }}</p>
        <el-button @click="readFile">Hello</el-button>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import myMenus from './components/my_menus.vue'
import myCard from './components/my_card.vue'
import axios from 'axios'  // 安装axios后引入
import { Low } from 'lowdb'
import { JSONFile } from 'lowdb/node'
import { Tabulator } from 'tabulator-tables'
export default {
  data() {
    return {
      baseMenu: [{
        "name": "Coremark",
      }],
      nowMenuItem: {},
      Pages: [],
      Page: {},
      echartMainHeight: '600px',
      cards_0: [],
      cards_1: [],
      beforeMenuItem: [],
      versions: [],
      nowVersion: {},
      echartMainTotalValue: 1,
      filelist: [],
      code: "请读取文件"
    }
  },
  components: {
    myMenus,
    myCard
  },
  created: function () {
  },
  mounted: function () {
    let nowMenuItem = this.baseMenu[0]
    while (nowMenuItem.children) {
      nowMenuItem = nowMenuItem.children[0]
    }
    this.nowMenuItem = nowMenuItem

    axios.get('./filelist.json').then((res) => {
      console.log('res.data = ', res.data)
      this.filelist = res.data
    })
  },
  methods: {
    readFile() {
      axios.get(this.filelist[0].path).then((res) => {
        this.code = res.data
        console.log('res.data = ', res.data)
      })
    },
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

.echart_tooltip_cycle {
  margin: 0px;
  padding: 0px;
  line-height: 5px;
}

.picture {
  width: 3px;
  height: 3px;
  background-color: red;
  border-radius: 1.5px;
}
</style>
