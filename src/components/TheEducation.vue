<template>
  <div class="row">
    <div class="col-12">
      <h2>Education</h2>
    </div>
  </div>
  <div class="row">
    <div class="col-6">
      <the-chart :chart-data="education" :type="'education'"></the-chart>
    </div>
    <div class="col-6">
      <the-degree
        v-for="(degree, index) in education.education"
        :key="index"
        :degree="degree"
        :index="index"
      ></the-degree>
    </div>
  </div>
</template>

<script>
import TheDegree from "./TheDegree.vue";
import TheChart from "./TheChart.vue";

export default {
  components: {
    TheDegree,
    TheChart,
  },
  props: ["education"],
};
</script>
