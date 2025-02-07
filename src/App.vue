<template>
   <div class="dashboard">
        <MainTable />
        <LineChart />
    </div>
</template>

<script>
import MainTable from './components/MainTable.vue'
import LineChart from './components/LineChart.vue'

export default {
  name: 'App',
  components: {
    MainTable,
    LineChart
  }
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
