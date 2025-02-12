<template>
  <h1>Timeseries</h1>
  <div class="dashboard">
    <div class="filters-container">
      <DateRangePicker :startDate="startDate" :endDate="endDate" @update:startDate="startDate = $event"
        @update:endDate="endDate = $event" />
      <ShowHideColumns :visibility="visibility" @update:visibility="visibility = $event" />
    </div>
    <div class="table-and-chart-container">
      <div class="table-component">
        <MainTable :key="startDate + endDate" :data="filteredData" :loading="isLoading" :startDate="formattedStartDate"
        :endDate="formattedEndDate" :visibility="visibility" @update-data="updateTableData" />
      </div>
      <div class="chart-component">
        <LineChart :key="filteredData" :data="filteredData" :loading="isLoading" :visibility="visibility" />
      </div> 
    </div>

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
  display: block;
  justify-content: space-between;
  align-items: flex-start;
  gap: 20px;
  padding: 20px;
  height: 100vh;
  box-sizing: border-box;
}

.table-and-chart-container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start; /* Aligns them at the top */
  gap: 20px;
  width: 100%;
  height: 100vh;
  padding: 10px;
}

.table-component {
  flex: 0.4;
}

.chart-component {
  flex: 0.55;
}

body {
  background: linear-gradient(rgb(216 232 242), rgb(79 112 156));
  background-attachment: fixed; /* Ensures the gradient stays in place */
  background-size: cover; /* Covers the entire viewport */
  font-family: "Arial", sans-serif;
}

h1 {
  font-size: 3rem;
  font-family: "Arial", sans-serif;
  text-align: center;
  color: #49708a;
}

/* responsiveness for smaller screens, vertical layout*/
@media (max-width: 768px) {
  .table-and-chart-container {
    flex-direction: column;
    align-items: center;
  }

  .table-component,
  .chart-component {
    width: 100%;
    min-width: unset;
  }
}
</style>
