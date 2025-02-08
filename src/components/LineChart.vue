<template>
    <div class="chart-container">
        <div v-if="loading" class="spinner-container">
            <div class="spinner"></div>
        </div>
        <canvas ref="chartCanvas"></canvas>
    </div>
</template>

<script>
import Chart from "chart.js/auto";

export default {
    props: {
        data: {
            type: Array,
            required: true,
        },
        loading: {
            type: Boolean,
            required: true,
        },
    },
    data() {
        return {
            chartInstance: null, // Store the chart instance
        };
    },
    watch: {
        // Re-render chart when data changes
        data: {
            handler() {
                this.renderChart();
            },
            deep: true, // Detects changes inside objects of data array
        },
    },
    mounted() {
        this.renderChart();
    },
    beforeUnmount() {
        if (this.chartInstance) {
            this.chartInstance.destroy(); // Cleanup chart before component is destroyed
        }
    },
    methods: {
        async renderChart() {
            try {
                if (!this.data.length) return; // Prevent empty chart

                const labels = this.data.map((item) => item.formattedDate);
                const datasetDE = this.data.map((item) => item.ENTSOE_DE_DAM_Price);
                const datasetGR = this.data.map((item) => item.ENTSOE_GR_DAM_Price);
                const datasetFR = this.data.map((item) => item.ENTSOE_FR_DAM_Price);

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
                            x: { title: { display: true, text: "Date and Time" } },
                            y: { title: { display: true, text: "Price" } },
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

.spinner-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 200px;
}

.spinner {
    width: 40px;
    height: 40px;
    border: 4px solid #ccc;
    border-top: 4px solid #49708a;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
</style>
