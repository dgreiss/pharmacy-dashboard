<template>
  <div class="chart w-full mx-auto" ref="chart" />
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
  },
  watch: {
    chartData: function() {
      const margin = { top: 20, right: 20, bottom: 30, left: 60 };
      const w = this.$refs.chart.offsetWidth;
      const h = 500;

      // Parse Data
      const data = this.chartData;
      let parsed = this.applySchema(data);
      // Scales
      const xScale = this.xScale(parsed, w, margin);
      const yScale = this.yScale(parsed, h, margin);
      // Axes
      const xAxis = this.xAxis(xScale);
      const yAxis = this.yAxis(yScale);
      // yhat line
      const yhat = this.yhat(xScale, yScale);
      // Draw Graph
      let svg = this.createSVG(w, h, margin);
      let yhat_path = this.drawYhat(svg, parsed, yhat);
      let make_y_gridlines = this.make_y_gridlines(yScale);
      let make_x_gridlines = this.make_x_gridlines(xScale);
      // Draw independent graph components
      this.drawAxes(svg, h, margin, xAxis, yAxis);
      this.animate(yhat_path);
      this.draw_y_gridlines(svg, make_y_gridlines, w, margin);
      this.draw_x_gridlines(svg, make_x_gridlines, h, margin);

      svg.append("text")
        .attr("x", (w - margin.left) / 2)
        .attr("y", margin.bottom / 2)
        .attr("text-anchor", "middle")
        .style("font-size", "16px")
        .text("Prescription Sales");

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
      return d3.scaleTime()
        .domain(d3.extent(data, function(d) { return d.ds }))
        .range([0, w - margin.left - margin.right]);
    },
    yScale: function(data, h, margin) {
      return d3.scaleLinear()
        .domain(d3.extent(data, function(d) { return d.yhat }))
        .range([h - margin.bottom - margin.top, 0])
        .nice();
    },
    xAxis: function(xScale) {
      return d3.axisBottom()
        .scale(xScale)
        .tickFormat(d3.timeFormat("%Y-%m-%d"));
    },
    yAxis: function(yScale) {
      return d3.axisLeft()
        .scale(yScale)
        .tickFormat(function(d) { return "$" + d3.format(".2s")(d) });
    },
    yhat: function(xScale, yScale) {
      return d3.line()
        .x(function(d) { return xScale(d.ds) })
        .y(function(d) { return yScale(d.yhat) });
    },
    createSVG: function(w, h, margin) {
      return d3.select(".chart")
        .classed("svg-container", true)
        .style("padding-bottom", h / w * 100 + "%")
        // .append("svg")
        // .attr("preserveAspectRatio", "xMinYMin meet")
        // .attr("viewBox", "0 0 1200 500")
        // .classed("svg-content-responsive", true)
        .append("svg")
        .attr("preserveAspectRatio", "xMinYMin meet")
        .attr("viewBox", "0 0 " + w + " " + h)
        // .attr("width", w)
        // .attr("height", h)
        .classed("svg-content-responsive", true)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
    },
    drawYhat: function(svg, data, yhat) {
      return svg.append("path")
        .datum(data)
        .attr("d", yhat)
        .attr("fill", "none")
        .attr("stroke", "blue");
    },
    drawAxes: function(svg, h, margin, xAxis, yAxis) {
      // Draw x Axis
      svg.append("g")
        .attr("transform", "translate(0," + (h - margin.top - margin.bottom) + ")")
        .call(xAxis);

      // Dray y Axis
      svg.append("g")
        .call(yAxis);
    },
    animate: function(path) {
      let totalLength = path.node().getTotalLength();
      return path
        .attr("stroke-dasharray", totalLength + " " + totalLength)
        .attr("stroke-dashoffset", totalLength)
        .transition()
        .duration(4000)
        .ease(d3.easeLinear)
        .attr("stroke-dashoffset", 0);
    },
    make_y_gridlines: function(yScale) {
      return d3.axisLeft(yScale)
        .ticks(5);
    },
    draw_y_gridlines: function(svg, make_y_gridlines, w, margin) {
      return svg.append("g")
        .attr("class", "grid")
        .call(make_y_gridlines
          .tickSize(-(w - margin.left - margin.right))
          .tickFormat("")
        );
    },
    make_x_gridlines: function(xScale) {
      return d3.axisBottom(xScale)
        .ticks(5);
    },
    draw_x_gridlines: function(svg, make_x_gridlines, h, margin) {
      return svg.append("g")
        .attr("class", "grid")
        .attr("transform", "translate(0," + (h - margin.bottom - margin.top) + ")")
        .call(make_x_gridlines
          .tickSize(-(h - margin.bottom - margin.top))
          .tickFormat("")
        );
    },

  }
}

</script>
<style>
.grid line {
  stroke: lightgrey;
  stroke-opacity: 0.6;
  shape-rendering: crispEdges
}

.svg-container {
  /*display: inline-block;*/
  position: relative;
  /*width: 100%;*/
  /*padding-bottom: 40%;*/
  /* aspect ratio */
  /*vertical-align: top;*/
  overflow: hidden;
}

.svg-content-responsive {
  /*display: inline-block;*/
  position: absolute;
  /*top: 10px;*/
  /*left: 0;*/
}

</style>
