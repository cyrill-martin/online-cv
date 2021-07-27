<template>
  <div
    class="life-station education"
    :style="{ 'background-color': hasFocus ? focusColor : 'white' }"
    :id="`education-${index}`"
    @mouseover="highlightEdu"
    @mouseout="highlightEdu"
  >
    <div class="degree-institution">{{ degree.institution }}</div>
    <div class="degree-degree">{{ degree.degree }}</div>
    <div class="degree-period">
      {{ degree.period.from }} – {{ degree.period.to }}
    </div>
    <div
      v-if="degree.summary"
      class="degree-summary"
      v-html="degree.summary"
    ></div>
  </div>
</template>

<script>
// import d3 from "./d3-importer.js";
import * as d3 from "d3";

export default {
  props: ["degree", "index"],
  inject: ["focusColor", "mobileWidth"],
  data() {
    return {
      hasFocus: false,
    };
  },
  methods: {
    changeEduFocus() {
      this.hasFocus = !this.hasFocus;
    },
    changeAreaFocus() {
      if (this.hasFocus) {
        d3.selectAll(".education-path").attr("fill-opacity", 0.1);
        d3.select(`#educationArea-${this.index}`).attr("fill-opacity", 1.0);
      } else {
        d3.selectAll(".education-path").attr("fill-opacity", 0.85);
      }
    },
    positionChart() {
      const chart = d3.select(`#education-chart`);
      if (this.hasFocus) {
        chart
          .style("position", "fixed")
          .style("top", "0")
          .style("left", "0")
          .style("z-index", "10")
          .style("background-color", "white")
        d3.select("#education-0")
          .style("margin-top", `${chart.style("height")}`);
      } else {
        chart
          .style("position", "relative")
          .style("top", "0")
          .style("left", "0")
          .style("z-index", "10")
          .style("background-color", null)
        d3.select("#education-0")
          .style("margin-top", `0px`);
      }
    },
    highlightEdu() {
      this.changeEduFocus();
      this.changeAreaFocus();
      if (window.innerWidth < this.mobileWidth) {
        this.positionChart();
      }
    },
  },
};
</script>

<style scoped>
.degree-institution {
  font-size: 1.2rem;
  font-weight: 700;
}

.degree-degree {
  font-weight: 700;
}

.degree-period {
  color: grey;
}

.degree-summary {
  margin: 1rem 0;
}
</style>
