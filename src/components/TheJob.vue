<template>
  <div
    class="life-station job"
    :style="{ 'background-color': hasFocus ? focusColor : 'white' }"
    :id="`job-${index}`"
    @mouseover="highlightJob"
    @mouseout="highlightJob"
    @touchstart="tryToHighlightJob"
    @touchend="tryToHighlightJob"
  >
  <!--  -->
    <div class="job-title">{{ job.title }}</div>
    <div class="job-company">
      <a :href="job.url" target="_blank">{{ job.company }}</a> · {{ job.type }}
    </div>
    <div class="job-period">{{ job.period.from }} – {{ job.period.to }}</div>
    <div class="job-summary" v-html="job.summary"></div>
    <div v-if="job.stack.length" class="job-stack">
      {{ job.stack.join(" · ") }}
    </div>
  </div>
</template>

<script>
// import d3 from "./d3-importer.js";
import * as d3 from "d3";

export default {
  props: ["job", "index"],
  inject: ["focusColor", "numberOfJobs", "transitionDuration", "mobileWidth"],
  data() {
    return {
      hasFocus: false,
    };
  },
  methods: {
    changeJobFocus() {
      this.hasFocus = !this.hasFocus;
    },
    changeAreaFocus() {
      if (this.hasFocus) {
        d3.selectAll(".job-path").attr("fill-opacity", 0.1);
        d3.select(`#jobArea-${this.index}`).attr("fill-opacity", 1.0);
      } else {
        d3.selectAll(".job-path").attr("fill-opacity", 0.85);
      }
    },
    getMarginTop() {
      const job = document.getElementById(`job-${this.index}`);
      const chart = document.getElementById("job-chart");
      const jobPos = job.offsetTop - document.body.scrollTop;
      const newChartPos =
        jobPos - (chart.offsetHeight / 2 - job.offsetHeight / 2);
      if (newChartPos > 0) {
        return newChartPos;
      } else {
        return 0;
      }
    },
    setChartMargin() {
      d3.select(`#job-chart`)
        .transition()
        .duration(this.transitionDuration)
        .style("margin-top", `${this.getMarginTop()}px`);
    },
    positionChart() {
      const chart = d3.select(`#job-chart`);
      if (this.hasFocus) {
        chart
          .style("position", "fixed")
          .style("top", "0")
          .style("left", "0")
          .style("z-index", "10")
          .style("background-color", "white")
        d3.select("#job-0")
          .style("margin-top", `${chart.style("height")}`);
      } else {
        chart
          .style("position", "relative")
          .style("top", "0")
          .style("left", "0")
          .style("z-index", "10")
          .style("background-color", null)
        d3.select("#job-0")
          .style("margin-top", `0px`);
      }
    },
    tryToHighlightJob() {
      if (window.innerWidth < this.mobileWidth) {

        this.highlightJob();
      }
    },
    highlightJob() {
      this.changeJobFocus();
      this.changeAreaFocus();
      if (window.innerWidth >= this.mobileWidth) {
        this.setChartMargin();
      } else {
        this.positionChart();
      }
    },
  },
};
</script>

<style scoped>
.job-title {
  font-size: 1.2rem;
  font-weight: 700;
}

.job-company {
  font-weight: 700;
}

.job-period {
  color: grey;
}

.job-summary {
  margin: 1rem 0;
}
</style>
