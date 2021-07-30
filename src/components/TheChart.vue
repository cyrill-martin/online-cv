<template>
  <div class="chart" :id="`${type}-chart`"></div>
</template>

<script>
// import d3 from "./d3-importer.js";
import * as d3 from "d3";

export default {
  props: ["chartData", "type"],
  inject: ["focusColor", "transitionDuration", "mobileWidth", "areaOpacity"],
  mounted() {
    // Draw the box plots
    this.drawD3(this.chartData);
  },
  methods: {
    drawD3(chartData) {
      // Set dimensions and other parameters
      let dimensions = {
        width: 800,
        heigth: 800,
        margins: {
          top: 120,
          right: 120,
          bottom: 120,
          left: 120,
        },
      };

      dimensions.ctrWidth =
        dimensions.width - (dimensions.margins.left + dimensions.margins.right);
      dimensions.ctrHeight =
        dimensions.heigth -
        (dimensions.margins.top + dimensions.margins.bottom);

      const parameters = {
        levels: 3, // Number of inner circles to be drawn
        areaOpacity: this.areaOpacity, // Opacity of the spider area
        fontSize: "12",
      };

      parameters.circleRadius = dimensions.ctrWidth / 2;

      const maxValue = 1;

      // xScale
      const xScale = chartData.areas;

      // yScale
      const circleSlice = (Math.PI * 2) / xScale.length; //The width in radians of each "slice"

      const yScale = d3
        .scaleLinear()
        .range([0, parameters.circleRadius])
        .domain([0, maxValue]);

      // Draw svg image
      const svg = d3
        .select(`#${this.type}-chart`)
        .style("margin-top", 0)
        .append("svg")
        .attr("class", "svg-image")
        .attr("id", `svg-${this.type}`)
        .attr("viewBox", `0 0 ${dimensions.width} ${dimensions.heigth}`);

      // The arc generator
      const radarArc = d3
        .arc()
        .innerRadius(0)
        .outerRadius((_, _2, factor) => yScale(maxValue * factor))
        .startAngle((_, i) => circleSlice * i - circleSlice / 2) // !
        .endAngle((_, i) => circleSlice * (i + 1) - circleSlice / 2); // !

      // The radial line generator
      // Used to draw the data paths/spider areas
      const radarLine = d3
        .lineRadial()
        .curve(d3.curveCardinalClosed)
        .radius((d) => yScale(d))
        .angle((_, i) => i * circleSlice);

      // Add container to svg image (consider margins)
      const ctr = svg
        .append("g")
        .attr(
          "transform",
          `translate(${dimensions.margins.left}, ${dimensions.margins.top})`
        );

      // Add g for the yScale
      const grid = ctr
        .append("g")
        .attr("class", "y-grid")
        .attr(
          "transform",
          `translate(${parameters.circleRadius}, ${dimensions.ctrWidth / 2})`
        );

      const xTicks = grid
        .selectAll(".x-tick")
        .data(xScale)
        .join("g")
        .attr("class", "x-tick");

      // Draw the x-axis tick labels
      xTicks
        .append("text")
        .attr("class", "x-tick-label")
        .style("font-size", parameters.fontSize)
        .each(function(d, i) {
          const centroid = radarArc.centroid(d, i, 2.4);
          d3.select(this)
            .attr("x", centroid[0])
            .attr("y", centroid[1])
            .text((d) => d)
            .attr("fill", "#333447");
        })
        .attr("text-anchor", "middle")
        .lower();

      // yScale circles
      grid
        .selectAll(".y-circle")
        .data(d3.range(1, parameters.levels + 1).reverse())
        .join("circle")
        .attr("class", "y-circle")
        .attr("r", (d) => (parameters.circleRadius / parameters.levels) * d)
        .attr("fill", "none")
        .attr("stroke", "lightgrey");

      const colors = (index) => {
        // const colors = ["34534C", "4C81AF", "CE8E38", "92B60A", "CF8865"]; // PHOTO
           const colors = ["58ABC9", "EC6A93", "F2EB0B", "EF8A61", "8BC63F"]; // kmapper k
        return `#${colors[index % colors.length]}`;
      };

      // Create a wrapper for the areas
      const spiderAreas = grid
        .selectAll(".spider-area")
        .data(chartData[this.type])
        .join("g")
        .attr("class", "spider-area")
        .attr("opacity", parameters.areaOpacity);

      // 'this' is going to be occupied
      const thisType = this.type;
      const thisFocusColor = this.focusColor;
      const thisDuration = this.transitionDuration;
      const thisMobileWidth = this.mobileWidth;
      const getMarginTop = (type, index) => {
        const item = document.getElementById(`${type}-${index}`);
        const chart = document.getElementById(`${type}-chart`);
        return chart.offsetHeight / 2 - item.offsetHeight / 2;
      };

      // Create the spider areas
      spiderAreas
        .append("path")
        .attr("class", `${this.type}-path`)
        .attr("id", (_, i) => `${this.type}Area-${i}`)
        .attr("d", (d) => {
          return radarLine(d.areaValues);
        })
        .attr("stroke", "none")
        .attr("fill", (_, i) => colors(i))
        .attr("fill-opacity", parameters.areaOpacity)
        .on("mouseover", function(e) {
          if (window.innerWidth >= thisMobileWidth) {
            // "Hide" all spider areas
            d3.selectAll(`.${thisType}-path`).attr("fill-opacity", "0.1");
            // Highlight current area
            d3.select(this).attr("fill-opacity", "1");
            // Get index of current area
            const index = d3
              .select(e.currentTarget)
              .attr("id")
              .split("-")[1];
            // Hide all item descriptions
            d3.selectAll(`.${thisType}`).style("display", "none");
            // Show and position currently corresponding item description next to spider chart
            d3.select(`#${thisType}-${index}`)
              .style("background-color", thisFocusColor)
              .style("display", "block")
              .style("margin-top", () => {
                if (window.innerWidth >= thisMobileWidth) {
                  return `${getMarginTop(thisType, index)}px`;
                } else {
                  return "0px";
                }
              });
            // Scroll to corresponding section
            document
              .getElementById(`sec-${thisType}`)
              .scrollIntoView({ behavior: "smooth" });
          }
        })
        .on("mouseout", function(e) {
          if (window.innerWidth >= thisMobileWidth) {
            // Show all spider areas
            d3.selectAll(`.${thisType}-path`).attr(
              "fill-opacity",
              parameters.areaOpacity
            );
            // Get index of current area
            const index = d3
              .select(e.currentTarget)
              .attr("id")
              .split("-")[1];
            // Show all item descriptions
            d3.selectAll(`.${thisType}`).style("display", "block");
            // Set back margin-top
            d3.select(`#${thisType}-${index}`)
              .style("background-color", "white")
              .transition()
              .duration(thisDuration)
              .style("margin-top", "0px");
          }
        })
        .on("click", function(e) {
          if (window.innerWidth < thisMobileWidth) {
            // Check if already selected
            const hasFocus = d3.select(this).attr("fill-opacity") === "1";

            // Get index of current area
            const index = d3
              .select(e.currentTarget)
              .attr("id")
              .split("-")[1];

            if (hasFocus) {
              // When selected
              // Show all spider areas
              d3.selectAll(`.${thisType}-path`).attr(
                "fill-opacity",
                parameters.areaOpacity
              );
              // Hide all item descriptions
              d3.selectAll(`.${thisType}`)
                .style("display", "block")
                .style("background-color", "white");
            } else {
              // When not selected
              // "Hide" all spider areas
              d3.selectAll(`.${thisType}-path`).attr("fill-opacity", "0.1");
              // Highlight current area
              d3.select(this).attr("fill-opacity", "1");
              // Hide all item descriptions
              d3.selectAll(`.${thisType}`)
                .style("display", "none")
                .style("background-color", "white");
              // Show only currently selected item description
              d3.selectAll(`#${thisType}-${index}`)
                .style("display", "block")
                .style("background-color", thisFocusColor);
            }
          }
        });
    },
  },
};
</script>
