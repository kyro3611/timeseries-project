<template>
  <div class="dashboard">
    <div class="date-range-and-table">
      <DateRangePicker :startDate="startDate" :endDate="endDate" @update:startDate="startDate = $event"
        @update:endDate="endDate = $event" />

        <ShowHideColumns :visibility="visibility" @update:visibility="visibility = $event"/>

      <MainTable :key="startDate + endDate" :data="filteredData" :loading="isLoading" :startDate="formattedStartDate"
        :endDate="formattedEndDate" :visibility="visibility" @update-data="updateTableData" />
    </div>
    <LineChart :key="filteredData" :data="filteredData" :loading="isLoading" :visibility="visibility"/>
  </div>
</template>

<script>
import MainTable from './components/MainTable.vue'
import LineChart from './components/LineChart.vue'
import DateRangePicker from './components/DateRangePicker.vue'
import ShowHideColumns from './components/ShowHideColumns.vue'
import dayjs from 'dayjs'

export default {
  name: 'App',
  components: {
    MainTable,
    LineChart,
    DateRangePicker,
    ShowHideColumns
  },
  data() {
    return {
      timeseriesData: [],
      isLoading: true,
      startDate: null,
      endDate: null,
      visibility: {
          DE: true,
          GR: true,
          FR: true,
      },
    };
  },
  async created() {
    await this.fetchAndFormatData();
  },
  computed: {
    filteredData() {
      if (!this.startDate || !this.endDate) return this.timeseriesData;

      return this.timeseriesData.filter(item => {
        const itemDate = dayjs(item.DateTime);
        return itemDate.isAfter(dayjs(this.startDate).subtract(1, 'hour')) &&
          itemDate.isBefore(dayjs(this.endDate).add(1, 'day'));
      });
    },
    formattedStartDate() {
      return this.startDate ? dayjs(this.startDate).format("DD-MM-YYYY HH:mm") : "";
    },
    formattedEndDate() {
      return this.endDate ? dayjs(this.endDate).format("DD-MM-YYYY HH:mm") : "";
    },
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
    updateTableData({ index, key, newValue }) {
        this.timeseriesData[index][key] = parseFloat(newValue) || newValue;
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

.date-range-and-table {
  flex: 1;
}
</style>
