<template>
    <div class="paket-irit-container">
        <h3 class="chart-title">Paket Irit</h3>
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
    name: "PaketIritChart",
    components: { LineChart },
    data() {
        return {
            preprocessData: [],
            forecastData: [],
            currentYear: 2022
        };
    },
    computed: {
        mergedData() {
            const all = [...this.preprocessData, ...this.forecastData];
            return all.sort((a, b) => dayjs(a.x).isAfter(dayjs(b.x)) ? 1 : -1);
        },
        filteredData() {
            const all = [
                ...this.preprocessData.map(item => ({ ...item, source: 'history' })),
                ...this.forecastData.map(item => ({ ...item, source: 'forecast' }))
            ];

            if (this.currentYear === 'All') return all;

            return all.filter(item =>
                dayjs(item.x).year() === this.currentYear
            );
        }
    },
    mounted() {
        this.fetchPreprocessData();
        this.fetchForecastData(); // <-- fetch forecast on mount
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
                        .filter(item => item.item_name && item.item_name.toUpperCase().includes("PAKET IRIT"))
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
                if (result.success || result.status === "success") {
                    this.forecastData = result.data
                        .filter(item =>
                            item.item_name &&
                            item.item_name.toUpperCase() === "PAKET IRIT" &&
                            item.id_forecast >= 4 &&
                            item.id_forecast <= 4
                        )
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
                this.currentYear = 'All';
            }
        },
        prevYear() {
            if (this.currentYear === 'All') {
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
    width: 100%;
    max-width: 1000px;
    margin: 0 auto;
    padding: 16px;
    box-sizing: border-box;
}

.chart-title {
    color: black;
    font-weight: bold;
    font-size: 18px;
    margin-bottom: 12px;
    text-align: center;
}

.year-nav {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 12px;
    margin-bottom: 20px;
    flex-wrap: wrap;
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
    width: 100%;
    max-width: 900px;
    margin: 0 auto;
    height: auto;
    aspect-ratio: 2 / 1;
    /* Menyesuaikan tinggi chart secara proporsional */
    position: relative;
}

/* Fallback jika browser tidak support aspect-ratio */
@supports not (aspect-ratio: 2 / 1) {
    .chart-wrapper {
        height: 300px;
    }
}

/* Responsif untuk perangkat kecil */
@media (max-width: 768px) {
    .chart-title {
        font-size: 16px;
    }

    button {
        font-size: 11px;
        padding: 5px 10px;
    }

    .year-label {
        font-size: 13px;
    }
}
</style>
