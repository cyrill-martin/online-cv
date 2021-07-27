<template>
  <div class="row">
    <div class="col-12">
      <h2>Experience</h2>
    </div>
  </div>
  <div class="row" @mouseout="resetChartMargin">
    <div class="col-6">
      <the-chart :chart-data="experience" :type="'job'"></the-chart>
    </div>
    <div class="col-6">
      <the-job
        v-for="(job, index) in experience.job"
        :key="job.title"
        :job="job"
        :index="index"
      ></the-job>
    </div>
  </div>
</template>

<script>
// import d3 from "./d3-importer.js";
import * as d3 from "d3";
import TheJob from "./TheJob.vue";
import TheChart from "./TheChart.vue";

export default {
  components: {
    TheJob,
    TheChart,
  },
  props: ["experience"],
  inject: ["transitionDuration"],
  methods: {
    resetChartMargin() {
      d3.select(`#job-chart`)
        .transition()
        .delay(this.transitionDuration)
        .duration(this.transitionDuration)
        .style("margin-top", "0px");
    },
  },
};
</script>

<style>
.chart-tooltip {
  padding: 5px;
  display: none;
}
</style>
