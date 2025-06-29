<template>
    <div class="paket-irit-container">
        <h3 class="chart-title">Paket Combo</h3>
        <!-- Navigasi Tahun (← →) -->
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
    name: "PaketComboChart",
    components: { LineChart },
    data() {
        return {
            allData: [], // gabungan preprocess + forecast
            currentYear: 2022
        };
    },
    computed: {
        filteredData() {
            if (this.currentYear === 'All') {
                return this.allData;
            }
            return this.allData.filter(item => dayjs(item.x).year() === this.currentYear);
        }
    },
    mounted() {
        this.fetchAllData();
    },
    methods: {
        showAllData() {
            this.currentYear = 'All';
        },
        async fetchAllData() {
            try {
                const preprocessRes = await fetch("http://127.0.0.1:8000/api/preprocess-transaksi");
                const preprocessJson = await preprocessRes.json();
                const preprocessData = preprocessJson.success
                    ? preprocessJson.data
                        .filter(item => item.item_name && item.item_name.toUpperCase().includes("PAKET COMBO"))
                        .map(item => ({
                            x: dayjs(item.week_start).format("YYYY-MM-DD"),
                            y: item.qty,
                            isForecast: false
                        }))
                    : [];

                const forecastRes = await fetch("http://127.0.0.1:8000/api/forecast-detail2");
                const forecastJson = await forecastRes.json();
                let forecastData = [];

                if (forecastJson.status === "success") {
                    forecastData = forecastJson.data
                        .filter(item => item.item_name && item.item_name.toUpperCase() === "PAKET COMBO")
                        .map(item => ({
                            x: dayjs(item.forecast_date).format("YYYY-MM-DD"),
                            y: item.qty,
                            isForecast: true
                        }));
                }

                // Gabungkan dan urutkan berdasarkan tanggal
                this.allData = [...preprocessData, ...forecastData].sort((a, b) =>
                    dayjs(a.x).isAfter(dayjs(b.x)) ? 1 : -1
                );

                if (this.allData.length) {
                    this.currentYear = dayjs(this.allData.at(-1).x).year();
                }

            } catch (err) {
                console.error("Gagal fetch data:", err);
            }
        }
        ,
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
  text-align: center;
  margin-bottom: 12px;
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
  aspect-ratio: 2 / 1; /* Responsive height via aspect ratio */
  position: relative;
}

/* Untuk fallback jika browser belum mendukung aspect-ratio */
@supports not (aspect-ratio: 2 / 1) {
  .chart-wrapper {
    height: 300px;
  }
}

/* Responsive adjustments for small screens */
@media (max-width: 768px) {
  .chart-title {
    font-size: 16px;
    padding-left: 0;
  }

  .chart-wrapper {
    max-width: 100%;
  }

  .year-nav {
    flex-direction: column;
    gap: 6px;
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
