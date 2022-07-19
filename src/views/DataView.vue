<template>
  <div class="plots-wrapper">
    <Plot
      @done-building="sync_cameras"
      class="plot"
      v-for="plot in plots"
      :id="plot"
    />
  </div>
  <button @click="plots.push(`plot${plots.length + 1}`)">ADD PLOT</button>
</template>

<script setup>
import { ref } from "vue";
import Plot from "@/components/Plot.vue";
import { relayout } from "plotly.js-dist-min";

const plots = ref(["plot1", "plot2"]);

const sync_cameras = (plotId) => {
  console.log(plotId);
  for (let i = plots.value.indexOf(plotId) - 1; i >= 0; i--) {
    sync_camera(plots.value[i], plotId);
    sync_camera(plotId, plots.value[i]);
  }
};

let cameraChange = [];
const sync_camera = (plot1Id, plot2Id) => {
  const plot1 = document.getElementById(plot1Id);
  const plot2 = document.getElementById(plot2Id);
  console.log(plot1);
  plot1.on("plotly_relayout", () => {
    if (!cameraChange.includes(plot2Id)) {
      console.log("change");
      cameraChange.push(plot2Id);
      relayout(plot2, { "scene.camera": plot1.layout.scene.camera }).then(
        () => {
          cameraChange = [];
        }
      );
    }
  });
};
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
