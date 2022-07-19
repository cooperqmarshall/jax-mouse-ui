<script setup>
import { ref, onMounted, watch } from "vue";
import { newPlot, deleteTraces, addTraces } from "plotly.js-dist-min";

const props = defineProps(["id", "data", "layout", "config"]);
const emit = defineEmits(["doneBuilding"]);

onMounted(async () => {
  await newPlot(props.id, props.data, props.layout, props.config);
  emit("doneBuilding", props.id);
});

watch(
  () => props.data,
  (newVal, oldVal) => {
    deleteTraces(props.id, [...Array(oldVal.length).keys()]);
    addTraces(props.id, newVal);
  }
);
</script>

<template>
  <div :id="id"></div>
</template>

<style scoped></style>
