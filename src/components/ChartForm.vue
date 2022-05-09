<template>
  <form @submit.prevent action="" class="date-form">
    <div class="form__item">
      <label class="form__label" for="data-selector">Данные</label>
      <select
        v-model="sensorType"
        name="data-selector"
        id="data-type"
        class="form__selector"
      >
        <option selected disabled value="">Выберите тип данных</option>
        <option value="temperature">Температура</option>
        <option value="visitors">Посетители</option>
        <option value="air-cuality">Качество воздуха</option>
      </select>
    </div>
    <div class="form__item">
      <label class="form__label" for="sensor-selector">Датчик</label>
      <select
        v-model="sensorId"
        name="sensor-selector"
        id="sensor"
        class="form__selector"
      >
        <option selected disabled value="">Выберите датчик</option>
        <option value="1">sens1</option>
        <option value="2">sens2</option>
        <option value="3">sens3</option>
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
// const data = require('src/assets/datasets/data1.json');
export default {
  props: {},
  data() {
    return {
      sensorType: "",
      sensorId: "",
      dateStart: "",
      dateEnd: "",
      DAY_IN_MS: 86400000,
    };
  },
  methods: {
    sendData() {
      this.$emit("sendData", {
        sensorId: this.sensorId,
        // sensorName:
        sensorType: this.sensorType,
        dateStart: this.dateStart,
        dateEnd: this.dateEnd,
        period: `${this.dateStart}:${this.dateEnd}`,
      });
      console.log(
        "Тип",
        this.sensorType,
        "id:",
        this.sensorId,
        "\nНачало периода",
        this.dateStart,
        "Конец периода",
        this.dateEnd
      );
    },
    getData() {
      console.log("data");
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
        this.sensorType &&
        this.sensorId &&
        this.dateStart &&
        this.dateEnd
      );
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
  },
};
</script>
