<template>
  <div class="plots-wrapper">
    <Plot class="plot" v-for="plot in plots" :id="plot" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import Plot from "@/components/Plot.vue";

const plots = ref(["plot1", "plot2"]);
</script>

<style scoped>
.plots-wrapper {
  display: flex;
}
.plot {
  width: 600px;
  padding: 1rem;
}
</style>
