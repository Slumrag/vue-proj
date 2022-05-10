<template>
  <Line :chart-options="options" />
</template>

<script>
import { Line } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  CategoryScale,
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  PointElement,
  CategoryScale
);

export default {
  name: "LineChart",
  components: { Line },

  data() {
    return {
      options: {
        elements: {
          point: {
            radius: 0,
          },
          line: {
            tension: 0.1,
          },
        },
        plugins: {
          legend: {
            display: false,
          },
        },
        scales: {
          yAxes: {
            grid: {
              display: false,
            },
          },
          xAxes: {
            grid: {
              display: true,
            },
          },
        },
        aspectRatio: 1.8,
        responsive: true,
        maintainAspectRatio: true,
      },
    };
  },
  mounted() {
    console.log("chart mounted");
  },
};
</script>