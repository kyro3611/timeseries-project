<template>
    <div class="chart-container">
        <div v-if="loading" class="spinner-container">
            <div class="spinner"></div>
        </div>
        <div v-if="!loading && !data.length" class="no-data-container">
            No Data for this date range
        </div>
        <canvas ref="chartCanvas"></canvas>
    </div>
</template>

<script>
import Chart from "chart.js/auto";
import { nextTick } from 'vue';

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
        visibility: {
            type: Object,
            required: true,
        }
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
        visibility: {
            handler() {
                this.renderChart();
            },
            deep: true,
        },
    },
    mounted() {
        this.renderChart();
    },
    beforeUnmount() {
        if (this.chartInstance) {
            this.chartInstance.destroy(); // Cleanup chart before component is destroyed
            this.chartInstance = null;
        }
    },
    methods: {
        async renderChart() {
            await nextTick();
            try {
                if (!this.data.length) return; // Prevent empty chart
                if (this.chartInstance) {
                    this.chartInstance.destroy();
                    this.chartInstance = null; // Reset instance
                }

                const ctx = this.$refs.chartCanvas.getContext("2d");

                const labels = this.data.map((item) => item.formattedDate);

                const datasets = [];

                if (this.visibility.DE) {
                    datasets.push({
                        label: "DE",
                        data: this.data.map(item => item.ENTSOE_DE_DAM_Price),
                        borderColor: "yellow",
                        fill: true,
                    });
                }
                if (this.visibility.GR) {
                    datasets.push({
                        label: "GR",
                        data: this.data.map(item => item.ENTSOE_GR_DAM_Price),
                        borderColor: "blue",
                        fill: true,
                    });
                }
                if (this.visibility.FR) {
                    datasets.push({
                        label: "FR",
                        data: this.data.map(item => item.ENTSOE_FR_DAM_Price),
                        borderColor: "red",
                        fill: true,
                    });
                }

                this.chartInstance = new Chart(ctx, {
                    type: "line",
                    data: {
                        labels,
                        datasets
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
        /*updateChart() {
            console.log('update')
            if (this.chartInstance && this.chartInstance.data.datasets.length > 0) {
                console.log('test', this.chartInstance.data.datasets)
                this.chartInstance.data.datasets.pop(); // Removes last dataset
                console.log('test', this.chartInstance.data.datasets)
                this.chartInstance.update(); // Refresh chart
            }
        }*/
    },
};
</script>

<style>
.no-data-container {
    margin-left: 20%;
    margin-top: 30%;
    font-size: 2vw;
}

.chart-container {
    height: 80%;
    position: relative;
    flex: 0.8;
    /* Takes 40% width */
    border: 4px solid #49708a;
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
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}
</style>
