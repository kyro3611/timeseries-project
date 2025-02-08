<template>
  <div class="dashboard">
    <MainTable :data="timeseriesData"/>
    <LineChart :data="timeseriesData"/>
  </div>
</template>

<script>
import MainTable from './components/MainTable.vue'
import LineChart from './components/LineChart.vue'
import dayjs from 'dayjs'

export default {
  name: 'App',
  components: {
    MainTable,
    LineChart
  },
  data() {
    return {
      timeseriesData: [],
    };
  },
  async created() {
    await this.fetchAndFormatData();
  },
  methods: {
    async fetchAndFormatData() {
      try {
        const response = await fetch("/timeseries.json");
        const json = await response.json();
        // Format DateTime
        this.timeseriesData = json.map((item) => ({
          ...item,
          formattedDate: dayjs(item.DateTime).format("DD-MM-YYYY HH:mm"),
        }));
      } catch (error) {
        console.error("Error loading JSON:", error);
      }
    },
  },
}
</script>

<style>
.dashboard {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 20px;
  padding: 20px;
  height: 100vh;
  box-sizing: border-box;
}
</style>
