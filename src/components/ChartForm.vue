<template>
  <form @submit.prevent action="" class="date-form">
    <!-- <div class="form__item">
      <label class="form__label" for="data-selector">Данные</label>
      <select
        v-model="selectedType"
        name="data-selector"
        id="data-type"
        class="form__selector"
      >
        <option selected disabled value="">Выберите тип данных</option>
    
        <option selected value="temperature">Температура</option>
        <option value="visitors">Посетители</option>
        <option value="air-cuality">Качество воздуха</option>
      </select>
    </div> -->
    <div class="form__item">
      <label class="form__label" for="sensor-selector">Датчик</label>
      <select
        v-model="selectedSensor"
        name="sensor-selector"
        id="sensor"
        class="form__selector"
      >
        <option selected disabled value="">Выберите датчик</option>
        <option v-for="(sensor, i) in sensors" :key="i" :value="sensor.id">
          {{ sensor.name }}
        </option>
      </select>
    </div>
    <div @change="correctDates" class="date-fields">
      <div class="form__item">
        <label class="form__label" for="date-start">Начало периода</label>
        <input
          v-model="dateStart"
          :max="formatDate(today)"
          type="date"
          name="date-start"
          id="date-begin"
          class="form__field"
        />
      </div>
      <div class="form__item">
        <label class="form__label" for="date-end">Конец периода</label>
        <input
          v-model="dateEnd"
          :max="formatDate(today)"
          type="date"
          name="date-end"
          id="date-end"
          class="form__field"
        />
      </div>
    </div>
    <!-- /.date-fields -->

    <!-- /.form--field -->
    <input
      @click="sendData"
      :disabled="isEmpty"
      type="submit"
      value="Получить данные"
      class="form__submit button"
    />
    <!-- /.data-form__submit button -->
  </form>
  <!-- /.date-form -->
</template>

<script>
export default {
  props: {},
  data() {
    return {
      // selectedType: "temperature",
      selectedSensor: "",
      dateStart: "",
      dateEnd: "",
      DAY_IN_MS: 86400000,
      sensors: [],
    };
  },
  methods: {
    sendData() {
      this.$emit("sendData", {
        selectedSensor: this.selectedSensor,
        // sensorName:
        selectedType: this.selectedType,
        dateStart: this.dateStart,
        dateEnd: this.dateEnd,
        period: `${this.dateStart}:${this.dateEnd}`,
      });
      console.log(
        "id:",
        this.selectedSensor,
        "\nНачало периода",
        this.dateStart,
        "Конец периода",
        this.dateEnd
      );
    },
    getSensors() {
      console.log("got sensors");
      return [
        {
          id: 1,
          name: "sens1",
          type: "temp",
        },
        {
          id: 2,
          name: "sens2",
          type: "visitors",
        },
        {
          id: 3,
          name: "sens2",
          type: "air",
        },
      ];
    },
    formatDate(date) {
      return date.toISOString().match(/[\d-]+/)[0];
    },
    compareDates(date1, date2) {
      const difference = Date.parse(date1) - Date.parse(date2);
      if (difference < 0) {
        return -1;
      } else if (difference > 0) {
        return 1;
      } else return 0;
    },
    correctDates() {
      if (this.compareDates(this.dateStart, this.dateEnd) > 0) {
        console.log(
          "dates swaped",
          this.compareDates(this.dateStart, this.dateEnd)
        );
        this.dateStart = this.formatDate(
          new Date(Date.parse(this.dateEnd) - this.DAY_IN_MS)
        );
      }
    },
  },
  computed: {
    today() {
      return new Date();
    },
    isEmpty() {
      return !(
        // this.selectedType &&
        (this.selectedSensor && this.dateStart && this.dateEnd)
      );
    },
    sensorTypes() {
      return this.sensors.map((item) => item.type);
    },
    selectedType() {
      return this.sensors.filter(
        (sensor) => sensor.id === this.selectedSensor
      )[0].type;
    },
    sensorNames() {
      return this.sensors.map((item) => item.name);
    },
  },
  mounted() {
    // console.log("mounted")
  },
  beforeMount() {
    const currDate = this.today;
    const weekBefore = new Date(currDate - this.DAY_IN_MS * 7);

    this.dateStart = this.formatDate(weekBefore);
    this.dateEnd = this.formatDate(currDate);
    this.sensors = this.getSensors();
  },
};
</script>
