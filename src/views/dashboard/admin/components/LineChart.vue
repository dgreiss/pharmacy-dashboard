<template>
  <div class="chart w-full" ref="chart" />
</template>
<script>
import * as d3 from 'd3';
import resize from './mixins/resize'

export default {
  mixins: [resize],
  props: {
    autoResize: {
      type: Boolean,
      default: true
    },
    chartData: {
      type: Array,
      required: true
    },
    width: {
      type: Number,

    }
  },
  mounted() {},
  watch: {
    chartData: function() {
      const margin = { top: 20, right: 20, bottom: 30, left: 60 };
      const w = this.$refs.chart.offsetWidth;
      const h = 500;

      const data = this.chartData;
      let parsed = this.applySchema(data);
      const xScale = this.xScale(parsed, w, margin);
      const yScale = this.yScale(parsed, h, margin);


      // const xScale = d3.scaleTime()
      //   .domain(d3.extent(parsed, function(d) { return d.ds }))
      //   .range([0, w - margin.left - margin.right]);

      // const yScale = d3.scaleLinear()
      //   .domain([d3.min(parsed, function(d) { return d.yhat_lower }),
      //     d3.max(parsed, function(d) { return d.yhat_upper; })
      //   ])
      //   .range([h - margin.bottom - margin.top, 0])
      //   .nice();

      const xAxis = d3.axisBottom()
        .scale(xScale)
        .tickFormat(d3.timeFormat("%Y-%m-%d"));

      const yAxis = d3.axisLeft()
        .scale(yScale)
        .tickFormat(function(d) { return "$" + d3.format(".2s")(d) });

      const yhat = d3.line()
        .x(function(d) { return xScale(d.ds) })
        .y(function(d) { return yScale(d.yhat) });

      const yhat_upper = d3.line()
        .x(function(d) { return xScale(d.ds) })
        .y(function(d) { return yScale(d.yhat_upper) });

      const yhat_lower = d3.line()
        .x(function(d) { return xScale(d.ds) })
        .y(function(d) { return yScale(d.yhat_lower) });

      // Create SVG
      let svg = d3.select(".chart")
        .append("svg")
        .attr("width", w)
        .attr("height", h)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      // Draw yhat
      let yhat_path = svg.append("path")
        .datum(parsed)
        .attr("d", yhat)
        .attr("fill", "none")
        .attr("stroke", "blue");
      var totalLength = yhat_path.node().getTotalLength();

      yhat_path
        .attr("stroke-dasharray", totalLength + " " + totalLength)
        .attr("stroke-dashoffset", totalLength)
        .transition()
        .duration(4000)
        .ease(d3.easeLinear)
        .attr("stroke-dashoffset", 0);

      // Draw yhat_upper
      svg.append("path")
        .datum(parsed)
        .attr("d", yhat_upper)
        .attr("fill", "none")
        .attr("stroke", "red");

      // Draw yhat_lower
      svg.append("path")
        .datum(parsed)
        .attr("d", yhat_lower)
        .attr("fill", "none")
        .attr("stroke", "green");

      //Draw Axes
      svg.append("g")
        .attr("transform", "translate(0," + (h - margin.top - margin.bottom) + ")")
        .call(xAxis);

      svg.append("g")
        .call(yAxis);

      // function make_x_gridlines() {
      // return d3.axisBottom(xAxis).ticks(8)
      // };

      // function make_y_gridlines() {
      // return d3.axisLeft(y).ticks(5)
      // };
      // svg.append("g")
      // .attr("class", "grid")
      // .attr("transform", "translate(0," + h + ")")
      // .style("stroke-dasharray", ("3,3"))
      // .call(make_x_gridlines()
      // // .tickSize(-h)
      // .tickFormat("")
      // )


    }
  },
  methods: {
    applySchema: function(data) {
      let parseTime = d3.timeParse("%Y-%m-%d");
      data.map(function(d) {
        d.ds = parseTime(d.ds);
        d.trend = +d.trend;
        d.trend_lower = +d.trend_lower;
        d.trend_upper = +d.trend_upper;
        d.yhat = +d.yhat;
        d.yhat_lower = +d.yhat_lower;
        d.yhat_upper = +d.yhat_upper;
      });
      return data;
    },
    xScale: function(data, w, margin) {
      const xScale = d3.scaleTime()
        .domain(d3.extent(data, function(d) { return d.ds }))
        .range([0, w - margin.left - margin.right]);
      return xScale;
    },
    yScale: function(data, h, margin) {
      const yScale = d3.scaleLinear()
        .domain([d3.min(data, function(d) { return d.yhat_lower }),
          d3.max(data, function(d) { return d.yhat_upper; })
        ])
        .range([h - margin.bottom - margin.top, 0])
        .nice();
      return yScale;
    }
  }
}

</script>
<style scoped>
.grid line {
  stroke: lightgrey;
  stroke-opacity: 0.6;
  shape-rendering: crispEdges
}

</style>
