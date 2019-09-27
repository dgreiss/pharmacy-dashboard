<template>
  <div>
    <div class="flex mb-2 ">
      <div class="w-1/4 shadow-md bg-white rounded-sm mr-6 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Monthly Rx Count</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ anm }}</div>
        </div>
      </div>
      <div class="w-1/4 shadow-md bg-white rounded-sm mx-3 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Monthly Sales</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ asm }}</div>
        </div>
      </div>
      <div class="w-1/4 shadow-md bg-white rounded-sm ml-6 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Monthly Gross Profit</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ agpm }}</div>
        </div>
      </div>
      <div class="w-1/4 shadow-md bg-white rounded-sm ml-6 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Monthly Gross Margin</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ agmm }}</div>
        </div>
      </div>
    </div>
    <div class="flex mb-8 ">
      <div class="w-1/4 shadow-md bg-white rounded-sm mr-6 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Annual Rx Count</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ any }}</div>
        </div>
      </div>
      <div class="w-1/4 shadow-md bg-white rounded-sm mx-3 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Annual Sales</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ asy }}</div>
        </div>
      </div>
      <div class="w-1/4 shadow-md bg-white rounded-sm ml-6 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Annual Gross Profit</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ agpy }}</div>
        </div>
      </div>
      <div class="w-1/4 shadow-md bg-white rounded-sm ml-6 ">
        <div class="w-5/6 mx-auto my-8">
          <div class="text-lg font-sans text-gray-700">Annual Gross Margin</div>
          <div class="text-2xl text-green-800 pt-1 font-bold">{{ agmy }}</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import * as d3 from 'd3';
import summary from '../summary.json';
console.log(summary);
export default {
  props: {
    // summary: [],
    // asm: null
  },
  data() {
    return {
      summary: {}
    }

  },
  created() {
    this.summary = summary.summary;
  },
  computed: {
    asm: function() {
      let asm = "$" + d3.format(".2s")(this.summary[0]["avg_sales_mnth"])
      return asm;
    },
    agpm: function() {
      let agpm = "$" + d3.format(".2s")(this.summary[0]["avg_gp_mnth"]);
      return agpm;
    },
    agmm: function() {
      let agmm = ((this.summary[0]["avg_gm_mnth"]) * 100).toFixed(1) + "%";
      return agmm;
    },
    anm: function() {
      let anm = (this.summary[0]["avg_n_mnth"]).toFixed(0);
      return anm;
    },
    asy: function() {
      let asy = "$" + d3.format(".2s")(this.summary[0]["avg_sales_yr"]);
      return asy;
    },
    agpy: function() {
      let agpy = "$" + d3.format(".2s")(this.summary[0]["avg_gp_yr"]);
      return agpy;
    },
    agmy: function() {
      let agmy = ((this.summary[0]["avg_gm_yr"]) * 100).toFixed(1) + "%";
      return agmy;
    },
    any: function() {
      let any = (this.summary[0]["avg_n_yr"]).toFixed(0);
      return any;
    },
  }
}

</script>
<style lang="scss" scoped>
.panel-group {
  margin-top: 18px;

  .card-panel-col {
    margin-bottom: 32px;
  }

  .card-panel {
    height: 108px;
    cursor: pointer;
    font-size: 12px;
    position: relative;
    overflow: hidden;
    color: #666;
    background: #fff;
    box-shadow: 4px 4px 40px rgba(0, 0, 0, .05);
    border-color: rgba(0, 0, 0, .05);

    &:hover {
      .card-panel-icon-wrapper {
        color: #fff;
      }

      .icon-people {
        background: #40c9c6;
      }

      .icon-message {
        background: #36a3f7;
      }

      .icon-money {
        background: #f4516c;
      }

      .icon-shopping {
        background: #34bfa3
      }
    }

    .icon-people {
      color: #40c9c6;
    }

    .icon-message {
      color: #36a3f7;
    }

    .icon-money {
      color: #f4516c;
    }

    .icon-shopping {
      color: #34bfa3
    }

    .card-panel-icon-wrapper {
      float: left;
      margin: 14px 0 0 14px;
      padding: 16px;
      transition: all 0.38s ease-out;
      border-radius: 6px;
    }

    .card-panel-icon {
      float: left;
      font-size: 48px;
    }

    .card-panel-description {
      float: right;
      font-weight: bold;
      margin: 26px;
      margin-left: 0px;

      .card-panel-text {
        line-height: 18px;
        color: rgba(0, 0, 0, 0.45);
        font-size: 16px;
        margin-bottom: 12px;
      }

      .card-panel-num {
        font-size: 20px;
      }
    }
  }
}

@media (max-width:550px) {
  .card-panel-description {
    display: none;
  }

  .card-panel-icon-wrapper {
    float: none !important;
    width: 100%;
    height: 100%;
    margin: 0 !important;

    .svg-icon {
      display: block;
      margin: 14px auto !important;
      float: none !important;
    }
  }
}

</style>
