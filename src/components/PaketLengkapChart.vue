<template>
    <div class="paket-irit-container">
        <h3 class="chart-title">Paket Lengkap</h3>
        <div class="year-nav">
            <button @click="prevYear">←</button>
            <span class="year-label">{{ currentYear }}</span>
            <button @click="nextYear">→</button>
        </div>
        <div class="chart-wrapper">
            <LineChart v-if="filteredData.length" :historyData="filteredData" :year="currentYear" />
        </div>
    </div>
</template>

<script>
import LineChart from "../Pages/LineChart.vue";
import dayjs from "dayjs";

export default {
    name: "PaketLengkapChart",
    components: { LineChart },
    data() {
        return {
            preprocessData: [],
            forecastData: [],
            forecastPoint: null,
            currentYear: 2022
        };
    },
    computed: {
        showForecast() {
            return this.currentYear === 2025 || this.currentYear === 'All';
        },
        mergedData() {
            const combined = this.showForecast
                ? [...this.preprocessData, ...this.forecastData]
                : this.preprocessData;
            return combined.sort((a, b) => dayjs(a.x).isAfter(dayjs(b.x)) ? 1 : -1);
        },
        filteredData() {
            if (this.currentYear === 'All') return this.mergedData;
            return this.mergedData.filter(item => dayjs(item.x).year() === this.currentYear);
        }
    },
    mounted() {
        this.fetchPreprocessData();
    },
    watch: {
        currentYear(newVal) {
            if (newVal === 2025 || newVal === 'All') {
                this.fetchForecastData();
            } else {
                this.forecastData = [];
            }
        }
    },
    methods: {
        showAllData() {
            this.currentYear = 'All';
        },
        async fetchPreprocessData() {
            try {
                const response = await fetch("http://127.0.0.1:8000/api/preprocess-transaksi");
                const result = await response.json();
                if (result.success) {
                    this.preprocessData = result.data
                        .filter(item => item.item_name && item.item_name.toUpperCase().includes("PAKET LENGKAP"))
                        .map(item => ({
                            x: dayjs(item.week_start).format("YYYY-MM-DD"),
                            y: item.qty
                        }));

                    if (this.preprocessData.length) {
                        const lastYear = dayjs(this.preprocessData.at(-1).x).year();
                        this.currentYear = lastYear;
                    }
                }
            } catch (err) {
                console.error("Gagal fetch preprocess:", err);
            }
        },
        async fetchForecastData() {
            try {
                const response = await fetch("http://127.0.0.1:8000/api/forecast-detail2");
                const result = await response.json();
                if (result.status === "success") {
                    this.forecastData = result.data
                        .filter(item => item.item_name && item.item_name.toUpperCase() === "PAKET LENGKAP")
                        .map(item => ({
                            x: dayjs(item.forecast_date).format("YYYY-MM-DD"),
                            y: item.qty
                        }));
                }
            } catch (err) {
                console.error("Gagal fetch forecast:", err);
            }
        },
        nextYear() {
            if (this.currentYear === 2022) {
                this.currentYear = 2023;
            } else if (this.currentYear === 2023) {
                this.currentYear = 2024;
            } else if (this.currentYear === 2024) {
                this.currentYear = 2025;
            } else if (this.currentYear === 2025) {
                this.currentYear = 'All';
            }
        },
        prevYear() {
            if (this.currentYear === 'All') {
                this.currentYear = 2025;
            } else if (this.currentYear === 2025) {
                this.currentYear = 2024;
            } else if (this.currentYear === 2024) {
                this.currentYear = 2023;
            } else if (this.currentYear === 2023) {
                this.currentYear = 2022;
            }
        }
    }
};
</script>

<style scoped>
.paket-irit-container {
    background-color: rgb(255, 255, 255);
    width: 1000px;
}

.chart-title {
    color: black;
    font-weight: bold;
    font-size: 18px;
    padding-left: 0px;
    margin-bottom: 12px;
    text-align: center;
}

.year-nav {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 12px;
    margin-bottom: 20px;
}

button {
    background-color: #eee;
    border: none;
    font-size: 12px;
    padding: 6px 12px;
    cursor: pointer;
    border-radius: 6px;
    transition: background-color 0.2s;
}

button:hover {
    background-color: #ccc;
}

.year-label {
    color: black;
    font-weight: bold;
    font-size: 14px;
}

.chart-wrapper {
    width: 900px;
    height: 250px;
}
</style>
