<template>
  <div class="dashboard-editor-container">
    <panel-group />
    <div class="bg-white px-4 pt-4 mb-8">
      <line-chart :chart-data="data" />
    </div>
  </div>
</template>
<script>
import * as d3 from 'd3';

import json from './data.json';

import PanelGroup from './components/PanelGroup';
import LineChart from './components/LineChart';

export default {
  name: 'DashboardAdmin',
  components: {
    PanelGroup,
    LineChart,
  },
  data() {
    return {
      data: [],
    }
  },
  mounted() {
    this.data = json.fcst;
  },
  methods: {
    // async fetchData() {
    // let response = await d3.json("/data.json");
    // this.data = response.fcst;
    // },
  }
}

</script>
<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: #F6F9FB;
  position: relative;


  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width:1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}

</style>
