<script setup>
import { ref, onMounted } from "vue";
import { newPlot } from "plotly.js-dist-min";

const props = defineProps(["id", "data", "layout", "config"]);

const plotlyId = ref(`plotly-${props.id}`);

onMounted(() => {
  console.log("built");
  newPlot(plotlyId.value, props.data, props.layout, props.config);
});
</script>

<template>
  <div :id="plotlyId"></div>
</template>

<style scoped></style>
