<template>
  <div>
    <template v-for="item in routerList">
      <el-sub-menu v-if="item.hasOwnProperty('children') && item.children.length > 0" :key="item.index">
        <template v-slot:title>
          <i class="el-icon-menu" style="padding-left:20px"></i>
          <span>{{ item.name }}</span>
        </template>
        <!--  如果有子级数据使用递归组件 -->
        <myMenus :routerList="item.children"></myMenus>
      </el-sub-menu>
      <el-menu-item v-else :key="item.index + 1" @click="clickMenuItem(item)">
        <i :class="item.icon" style="padding-left:20px"></i>
        <span>{{ item.name }}</span>
      </el-menu-item>
    </template>
  </div>
</template>
 
<script>

export default {
  props: ['routerList'],
  name: 'myMenus',
  created: function () {
    console.log(this.routerList)
  },
  methods: {
    clickMenuItem(val) {
      this.$emit('updataPages', val);//select事件触发后，自动触发showCityName事件
    }
  }
}

</script>
 
<style  lang="css">
.el-menu--collapse span,
.el-menu--collapse i.el-submenu__icon-arrow {
  height: 0;
  width: 0;
  overflow: hidden;
  visibility: hidden;
  display: inline-block;
}
</style>