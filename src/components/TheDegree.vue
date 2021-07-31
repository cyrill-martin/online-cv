<template>
  <div
    class="life-station education"
    :style="{ 'background-color': hasFocus ? focusColor : 'white' }"
    :id="`education-${index}`"
    @mouseover="highlightEdu"
    @mouseout="highlightEdu"
    @click="highlightEduMobile"
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
import d3 from "../d3-importer.js";

export default {
  props: ["degree", "index"],
  inject: ["focusColor", "mobileWidth", "areaOpacity"],
  created() {
    window.addEventListener("resize", this.clearFocus);
  },
  data() {
    return {
      hasFocus: false,
    };
  },
  methods: {
    clearFocus() {
      this.hasFocus = false;
    },
    changeEduFocus() {
      const hasFocus =
        d3.select(`#education-${this.index}`).style("background-color") ===
        this.focusColor;
      hasFocus ? (this.hasFocus = false) : (this.hasFocus = true);
      // this.hasFocus = !this.hasFocus;
    },
    changeAreaFocus() {
      if (this.hasFocus) {
        d3.selectAll(".education-path").attr("fill-opacity", "0.1");
        d3.select(`#educationArea-${this.index}`).attr("fill-opacity", "1");
      } else {
        d3.selectAll(".education-path").attr("fill-opacity", this.areaOpacity);
      }
    },
    highlightEdu() {
      if (window.innerWidth >= this.mobileWidth) {
        this.changeEduFocus();
        this.changeAreaFocus();
      }
    },
    highlightEduMobile() {
      if (window.innerWidth < this.mobileWidth) {
        // Scroll to corresponding section
        document
          .getElementById("sec-education")
          .scrollIntoView();
        this.changeEduFocus();
        this.changeAreaFocus();
        if (this.hasFocus) {
          // Hide all item descriptions
          d3.selectAll(`.education`)
            .style("display", "none")
            .style("background-color", "white");
          // Show only currently selected item description
          d3.select(`#education-${this.index}`)
            .style("display", "block")
            .style("background-color", this.focusColor);
        } else {
          // Show all item descriptions
          d3.selectAll(".education")
            .style("display", "block")
            .style("background-color", "white");
          // Scroll to corresponding section
          document
            .getElementById(`education-${this.index}`)
            .scrollIntoView();
        }
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
