<script setup>
import Plotly from "@/components/Plotly.vue";
import { onMounted, ref } from "vue";

const props = defineProps(["id"]);

const dataReady = ref(false);
const annotation = ref("Please select an annotation");
const annotation_options = ref(["test"]);
const data = ref([]);
const layout = ref({
  scene: {
    xaxis: {
      // visible: false
      showticklabels: false,
      title: { text: "UMAP 1" },
    },
    yaxis: {
      // visible: false
      showticklabels: false,
      title: { text: "UMAP 2" },
    },
    zaxis: {
      // visible: false
      showticklabels: false,
      title: { text: "UMAP 3" },
    },
  },
  height: 600,
  margin: { l: 0, r: 0, b: 0, t: 0 },
  legend: {
    bgcolor: "rgba(255,255,255,0.6)",
    yanchor: "top",
    y: 0.95,
    xanchor: "right",
    x: 0.99,
  },
  showlegend: true,
});

const fetch_data = async (params) => {
  let data = await fetch(
    `http://localhost:5000/gene_filtered?${new URLSearchParams(params)}`,
    {
      method: "GET",
      headers: {
        "Access-Control-Allow-Origin": "*",
      },
    }
  ).then((d) => d.json());
  return data.data;
};

const fetch_annotation_options = async () => {
  let data = await fetch(`http://localhost:5000/annotation_options`, {
    method: "GET",
    headers: {
      "Access-Control-Allow-Origin": "*",
    },
  }).then((d) => d.json());
  return data.options;
};

onMounted(async () => {
  annotation_options.value = await fetch_annotation_options();
  console.log(annotation_options);
  let api_data = await fetch_data({
    timepoint: "E9",
    gene: "ENSMUSG00000061024",
  });

  //api_data = [
  //  ["x", "y", "z", "expression"],
  //  [1, 1, 1, "1"],
  //  [0, 1, 1, "2"],
  //  [1, 0, 1, "4"],
  //];

  let cols = api_data[0];
  let annotation = "expression";
  let annotation_type = isNaN(parseInt(api_data[1][cols.indexOf(annotation)]))
    ? "categorical"
    : "scalar";

  const unpack = (d, i) => d.map((row) => row[i]);
  let unique = ["expression"];
  if (annotation_type == "categorical") {
    unique = [...new Set(unpack(api_data.slice(1), cols.indexOf(annotation)))];
  }

  let color = [];
  let traces = [];
  let filtered_api_data = api_data.slice(1);
  for (let i in unique) {
    if (annotation_type == "categorical") {
      filtered_api_data = api_data
        .slice(1)
        .filter((row) => row[cols.indexOf(annotation)] == unique[i]);
    } else if (annotation_type == "scalar") {
      color = unpack(filtered_api_data, cols.indexOf(unique[i]));
    }
    let trace = {
      name: unique[i],
      x: unpack(filtered_api_data, cols.indexOf("x")),
      y: unpack(filtered_api_data, cols.indexOf("y")),
      z: unpack(filtered_api_data, cols.indexOf("z")),
      mode: "markers",
      marker: {
        size: 3,
        color: color,
        width: 0.1,
        showscale: annotation_type == "scalar",
      },
      type: "scatter3d",
      hovertemplate: "Trace",
    };
    traces.push(trace);
  }

  data.value = [...traces];

  dataReady.value = true;
});
</script>

<template>
  <div v-if="dataReady" class="plot-wrapper">
    <div class="filters">
      <select name="annotation" v-model="annotation">
        <option disabled>Please select an annotation</option>
        <option v-for="opt in annotation_options">{{ opt }}</option>
      </select>
    </div>
    <Plotly
      :data="data"
      :layout="layout"
      :id="props.id"
      :config="{ responsive: true }"
    ></Plotly>
    <div class="title">Title</div>
  </div>
</template>

<style scoped></style>
