<template>
  <div
    class="life-station job"
    :style="{ 'background-color': hasFocus ? focusColor : 'white' }"
    :id="`job-${index}`"
    @mouseover="highlightJob"
    @mouseout="highlightJob"
    @click="highlightJobMobile"
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
import d3 from "../d3-importer.js";

export default {
  props: ["job", "index"],
  inject: [
    "focusColor",
    "numberOfJobs",
    "transitionDuration",
    "mobileWidth",
    "areaOpacity",
  ],
  data() {
    return {
      hasFocus: false,
    };
  },
  methods: {
    changeJobFocus() {
      const hasFocus =
        d3.select(`#job-${this.index}`).style("background-color") ===
        this.focusColor;
      hasFocus ? (this.hasFocus = false) : (this.hasFocus = true);
      // this.hasFocus = !this.hasFocus;
    },
    changeAreaFocus() {
      if (this.hasFocus) {
        d3.selectAll(".job-path").attr("fill-opacity", "0.1");
        d3.select(`#jobArea-${this.index}`).attr("fill-opacity", "1");
      } else {
        d3.selectAll(".job-path").attr("fill-opacity", this.areaOpacity);
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
    highlightJob() {
      if (window.innerWidth >= this.mobileWidth) {
        this.changeJobFocus();
        this.changeAreaFocus();
        this.setChartMargin();
      }
    },
    highlightJobMobile() {
      if (window.innerWidth < this.mobileWidth) {
        // Scroll to corresponding section
        document
          .getElementById("sec-job")
          .scrollIntoView();
        this.changeJobFocus();
        this.changeAreaFocus();
        if (this.hasFocus) {
          // Hide all item descriptions
          d3.selectAll(".job")
            .style("display", "none")
            .style("background-color", "white");
          // Show only currently selected item description
          d3.select(`#job-${this.index}`)
            .style("display", "block")
            .style("background-color", this.focusColor);
        } else {
          // Show all item descriptions
          d3.selectAll(".job")
            .style("display", "block")
            .style("background-color", "white");
          // Scroll to corresponding section
          document
            .getElementById(`job-${this.index}`)
            .scrollIntoView();
        }
      }
    },
  },
};
</script>

<style scoped>
.job-title {
  font-size: 1.1rem;
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

@media only screen and (min-width: 45em) {
  .job-title {
    font-size: 1.2rem;
  }
}
</style>
