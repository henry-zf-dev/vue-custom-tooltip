<template>
  <div id="app">
    <el-button content="tooltip 显示的内容" class="tooltip-content">显示 tooltip</el-button>
    <CustomTooltip
      ref="tooltipRef"
      :open-delay="800"
      :hide-arrow="false"
      :offset="[0, 10]"
      rely-class="tooltip-content"></CustomTooltip>
  </div>
</template>

<script>
import CustomTooltip from './components/CustomTooltip'

export default {
  name: 'App',
  components: {
    CustomTooltip
  },
  mounted () {
    const tooltip = this.$refs['tooltipRef']
    tooltip && tooltip.init()
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
