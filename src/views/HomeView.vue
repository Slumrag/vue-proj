<template>
  <header class="container">
    <nav-block />
  </header>
  <main class="container">
    <section class="flex-container">
      <chart-form @sendData="requestData" />
      <div class="chart-container">
        <h2 v-if="loaded" class="chart-title">{{ chartTitle }}</h2>
        <!-- /.chart-title -->
        <line-chart class="chart" v-if="loaded" :chart-data="dataset" />
        <!-- /.chart-cont -->
      </div>
      <!-- /.chart-container -->
      <!-- <bar-chart v-if="loaded" :chart-data="dataset" /> -->
    </section>
  </main>
  <hr />

  <footer class="container">
    <p class="footer-text">Проект мониторинга параметров здания "Монитор"</p>
    <!-- /.footer-text -->
  </footer>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import NavBlock from "@/components/NavBlock.vue";
import ChartForm from "@/components/ChartForm.vue";
import BarChart from "@/components/BarChart.vue";
import LineChart from "@/components/LineChart.vue";
export default {
  name: "HomeView",
  components: {
    NavBlock,
    ChartForm,
    // eslint-disable-next-line vue/no-unused-components
    BarChart,
    LineChart,
  },
  data() {
    return {
      dataPakage: "vue",
      dataPakageName: "",
      period: "",
      chartType: "",
      downloads: [],
      labels: [],
      loaded: false,
      showError: true,
      errorMessage: "Please enter a package",
    };
  },
  computed: {
    dataset() {
      return {
        labels: this.labels,
        datasets: [
          {
            label: "",
            borderColor: "#1C6DD0",
            pointBackgroundColor: "white",
            borderWidth: 1,
            pointBorderColor: "#1C6DD0",
            backgroundColor: "transparent",
            data: this.downloads,
          },
        ],
      };
    },
    chartTitle() {
      return `График ${this.chartType}`;
    },
  },
  mounted() {},
  methods: {
    getFormData(formData) {
      this.chartType = formData.selectedType;
      this.period = formData.period;
      console.log("got form data", formData, "\ntype", this.chartType);
    },
    resetState() {
      this.loaded = false;
      this.showError = false;
    },
    requestData(formData) {
      if (
        this.dataPakage === null ||
        this.dataPakage === "" ||
        this.dataPakage === "undefined"
      ) {
        this.showError = true;
        return;
      }
      this.resetState();
      this.getFormData(formData);

      axios
        .get(
          `https://api.npmjs.org/downloads/range/${this.period}/${this.dataPakage}`
        )
        .then((response) => {
          console.log(response.data);
          this.downloads = response.data.downloads.map(
            (download) => download.downloads
          );
          this.labels = response.data.downloads.map((download) => download.day);
          this.dataPakageName = response.data.dataPakage;
          this.loaded = true;
        })
        .catch((err) => {
          this.errorMessage = err.response.data.error;
          this.showError = true;
        });
    },
  },
};
</script>
