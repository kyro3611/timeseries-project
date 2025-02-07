<template>
    <div class="chart-container">
        <canvas ref="chartCanvas"></canvas>
    </div>
</template>

<script>
import Chart from "chart.js/auto";
import dayjs from "dayjs";

export default {
    data() {
        return {
            chartInstance: null, // Stores the chart instance
        };
    },
    mounted() {
        this.fetchAndRenderChart(); // Fetch data when component mounts
    },
    beforeUnmount() {
        if (this.chartInstance) {
            this.chartInstance.destroy(); // Cleanup chart before component is destroyed
        }
    },
    methods: {
        async fetchAndRenderChart() {
            try {
                const response = await fetch("/timeseries.json");
                const json = await response.json();

                const labels = json.map((item) =>
                    dayjs(item.DateTime).format("DD-MM HH:mm")
                );
                const datasetDE = json.map((item) => item.ENTSOE_DE_DAM_Price);
                const datasetGR = json.map((item) => item.ENTSOE_GR_DAM_Price);
                const datasetFR = json.map((item) => item.ENTSOE_FR_DAM_Price);

                if (this.chartInstance) this.chartInstance.destroy(); // Prevent duplicate charts

                this.chartInstance = new Chart(this.$refs.chartCanvas, {
                    type: "line",
                    data: {
                        labels,
                        datasets: [
                            {
                                label: "DE",
                                data: datasetDE,
                                borderColor: "yellow",
                                backgroundColor: "rgba(0, 0, 255, 0.1)",
                                fill: true,
                            },
                            {
                                label: "GR",
                                data: datasetGR,
                                borderColor: "blue",
                                backgroundColor: "rgba(0, 255, 0, 0.1)",
                                fill: true,
                            },
                            {
                                label: "FR",
                                data: datasetFR,
                                borderColor: "red",
                                backgroundColor: "rgba(255, 0, 0, 0.1)",
                                fill: true,
                            },
                        ],
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            legend: { display: true },
                        },
                        scales: {
                            x: { title: { display: true, text: "Time" } },
                            y: { title: { display: true, text: "Value" } },
                        },
                    },
                });
            } catch (error) {
                console.error("Error loading JSON:", error);
            }
        },
    },
};
</script>

<style>
.chart-container {
    height: 80%;
    position: relative;
    flex: 0.8; /* Takes 40% width */
}
</style>
