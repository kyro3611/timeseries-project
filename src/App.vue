<template>
  <div class="dashboard">
    <MainTable :data="timeseriesData" :loading="isLoading" />
    <LineChart :data="timeseriesData" :loading="isLoading" />
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
      isLoading: true
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
      finally {
        this.isLoading = false;
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
