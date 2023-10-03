<template>
  <el-col :span="24">
    <el-card class="box-card">
      <template #header>
        <div class="card-header">
          <span>{{ cardData.head }}</span>
        </div>
      </template>

      <!-- text -->
      <div v-if="cardData.type == 'text'" :style="{ height: cardData.height ? cardData.height : 'auto' }">{{ cardData.body
      }}</div>

      <!-- image -->
      <img v-else-if="cardData.type == 'image'" :src="cardData.path" :style="{
        height: cardData.height ? cardData.height : '100px',
        maxWidth: '100%'
      }" />

      <!-- gauge-percent -->
      <div v-else-if="cardData.type == 'gauge-percent'" :id="'card_gauge_percent_' + cardData.index"
        :style="{ height: cardData.height ? cardData.height : '300px' }" />

      <!-- gauge-percent -->
      <div v-else-if="cardData.type == 'gauge-number'" :id="'card_gauge_number_' + cardData.index"
        :style="{ height: cardData.height ? cardData.height : '300px' }" />

      <!-- gauge-percent -->
      <el-descriptions v-else-if="cardData.type == 'description'" :column=1 border>
        <template v-for="item in cardData.items" :key="item.label">
          <el-descriptions-item :label="item.label" v-if="item.isTag">
            <el-tag size="small">{{ item.value }}</el-tag>
          </el-descriptions-item>
          <el-descriptions-item :label="item.label" v-else>
            {{ item.value }}
          </el-descriptions-item>
        </template>
      </el-descriptions>
    </el-card>
  </el-col>

  <el-col :span="24" class="empty-col"></el-col>
</template>
 
<script>
import * as echarts from 'echarts'

var echartGlobal = []

export default {
  props: ['cardData'],
  name: 'myCard',
  created: function () {
    console.log(this.cardData)
  },
  mounted: function () {
    this.init()
  },
  updated: function () {
    console.info("updated")
    this.init()
  },
  methods: {
    init () {
      let divId, echartObj
      switch (this.cardData.type) {
        case 'gauge-percent':
          console.log("showChart gauge_percent")
          divId = 'card_gauge_percent_' + this.cardData.index
          echartObj = echarts.init(document.getElementById(divId))
          console.log("mycard", divId, echartObj, document.getElementById(divId))
          echartObj.setOption({
            tooltip: {
              formatter: '{a} <br/>{b} : {c}%'
            },
            series: [
              {
                name: 'Pressure',
                type: 'gauge',
                radius: '100%',
                progress: {
                  show: true
                },
                detail: {
                  valueAnimation: true,
                  fontSize: 24,
                  formatter: '{value}%'
                },
                startAngle: 210,
                endAngle: -30,
                data: [
                  {
                    value: this.cardData.percent ? this.cardData.percent : (this.cardData.part / this.cardData.total * 100).toFixed(2),
                    name: this.cardData.name
                  }
                ]
              }
            ]
          })
          break
        case 'gauge-number':
          console.log("showChart gauge_percent")
          divId = 'card_gauge_number_' + this.cardData.index
          echartObj = echarts.init(document.getElementById(divId))
          echartObj.setOption({
            tooltip: {
              formatter: '{a} <br/>{b} : {c}%'
            },
            series: [
              {
                name: 'Pressure',
                type: 'gauge',
                radius: '100%',
                progress: {
                  show: true
                },
                detail: {
                  valueAnimation: true,
                  fontSize: 24,
                  formatter: '{value}%'
                },
                min: this.cardData.min ? this.cardData.min : 0,
                max: this.cardData.max ? this.cardData.max : 100,
                startAngle: 210,
                endAngle: -30,
                data: [
                  {
                    value: this.cardData.value,
                    name: this.cardData.name
                  }
                ]
              }
            ]
          })
      }
    }
  }
}

</script>
 
<style  lang="css">
.empty-col {
  height: 10px;
}
</style>