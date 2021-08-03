<template>
  <section id="sec-job">
    <the-experience
      :experience="experience"
      @remove-edu-focus="removeFocus('edu')"
    ></the-experience>
  </section>

  <section id="sec-education">
    <the-education :education="education" :clear-focus="focusedEdu"></the-education>
  </section>
</template>

<script>
import TheExperience from "./stations/TheExperience.vue";
import TheEducation from "./stations/TheEducation.vue";

export default {
  components: {
    TheExperience,
    TheEducation,
  },
  props: ["experience", "education"],
  data() {
    return {
      focusedJob: null,
      focusedEdu: false,
    }
  },
  methods: {
    removeFocus(type) {
      if (type === "edu") {
        this.focusedEdu = false;
      }
    },
  },
};
</script>
