<template>
  <div class="dashboard-editor-container">
    <panel-group />
    <el-row class="bg-white px-4 pt-4 mb-8">
      <line-chart :chart-data="data" />
    </el-row>
    <el-row :gutter="32">
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
          <raddar-chart />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
          <pie-chart />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="8">
        <div class="chart-wrapper">
          <bar-chart />
        </div>
      </el-col>
    </el-row>
    <el-row :gutter="8">
      <el-col :xs="{span: 24}" :sm="{span: 24}" :md="{span: 24}" :lg="{span: 12}" :xl="{span: 12}" style="padding-right:8px;margin-bottom:30px;">
        <!-- <transaction-table /> -->
      </el-col>
    </el-row>
  </div>
</template>
<script>
import * as d3 from 'd3';

import json from './data.json';
// import summary from './summary.json';
// console.log(summary.summary[0]["avg_sales_mnth"]);


import PanelGroup from './components/PanelGroup';
import LineChart from './components/LineChart';
import RaddarChart from './components/RaddarChart';
import PieChart from './components/PieChart';
import BarChart from './components/BarChart';
// import TransactionTable from './components/TransactionTable';

export default {
  name: 'DashboardAdmin',
  components: {
    PanelGroup,
    LineChart,
    RaddarChart,
    PieChart,
    BarChart,
    // TransactionTable,
  },
  data() {
    return {
      data: [],
      // summary: []
    }
  },
  mounted() {
    this.data = json.fcst;
    // this.summary = summary.summary;
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
