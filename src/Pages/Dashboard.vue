<template>
  <div class="app-container">
    <Sidebar />

    <div class="main-content">
      <Navbar />

      <!-- area isi dashboard -->
      <div class="dashboard-content">
        <!-- Judul -->
        <div class="dashboard-title">
          <h1>FORECASTING</h1>
          <p class="subtitle">Mireta Backoffice</p>
        </div>
        <div class="chart-row">
          <PaketIritChart />
          <PaketComboChart />
        </div>
        <div class="chart-row">
          <PaketLengkapChart />
          <PaketKeluargaChart />
        </div>
        <PaketKhasduoChart />

      </div>
      <div class="tables-row">


        <div class="table-wrapper">
          <h3 class="table-title">Total Paket dan Varian</h3>
          <div class="table-scroll">
            <table class="order-table">
              <thead>
                <tr>
                  <th>Nama Paket</th>
                  <th>Total Paket</th>
                  <th>Varian Item</th>
                  <th>Total Varian Per Paket</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="paket in paketDetailData" :key="paket.item_name">
                  <td>{{ paket.item_name }}</td>
                  <td>{{ paket.qty }} paket</td>
                  <td>
                    <ul>
                      <li v-for="v in paket.varians" :key="v.varian_name">
                        {{ v.average_qty }} {{ v.varian_name }}
                      </li>
                    </ul>
                  </td>
                  <td>
                    <ul>
                      <li v-for="v in paket.varians" :key="v.varian_name">
                        {{ v.original_qty }} {{ v.varian_name }}
                      </li>
                    </ul>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>


        <div class="table-wrapper">
          <h3 class="table-title3">Total Seluruh Varian</h3>
          <div class="table-scroll">
            <table class="order-table">
              <thead>
                <tr>
                  <th>Varian Item</th>
                  <th>Total Varian</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="v in totalVarianSummary" :key="v.varian_name">
                  <td>{{ v.varian_name }}</td>
                  <td>{{ v.total_qty }} pcs</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

      </div>

    </div>
  </div>
</template>


<script>
import Sidebar from '../components/Sidebar.vue';
import Navbar from '../components/Navbar.vue';
import PaketIritChart from '../components/PaketIritChart.vue'; // path-nya sesuaikan
import PaketComboChart from '../components/PaketComboChart.vue';
import PaketLengkapChart from '../components/PaketLengkapChart.vue';
import PaketKeluargaChart from '../components/PaketKeluargaChart.vue';
import PaketKhasduoChart from '../components/PaketKhasduoChart.vue';


export default {
  components: {
    Sidebar,
    Navbar,
    PaketIritChart,
    PaketComboChart,
    PaketLengkapChart,
    PaketKeluargaChart,
    PaketKhasduoChart
  },
  data() {
    return {
      forecastData: [],
      varianData: [],
      paketDetailData: [],
      totalVarianSummary: [],
    };
  },
  mounted() {
    this.fetchForecastData();
    this.fetchVarianData();
    this.fetchPaketDetailData();
    this.fetchVariantSummary();
  },
  methods: {
    async fetchVariantSummary() {
      try {
        const response = await fetch("http://127.0.0.1:8000/api/variant-summary");
        const data = await response.json();

        if (data.status === "success") {
          this.totalVarianSummary = data.data;
        } else {
          console.warn("Gagal mengambil ringkasan varian: status bukan success");
        }
      } catch (error) {
        console.error("Gagal mengambil ringkasan varian:", error);
      }
    },
    computeVarianSummary() {
      const summary = {};

      this.paketDetailData.forEach(paket => {
        paket.varians.forEach(v => {
          if (!summary[v.varian_name]) {
            summary[v.varian_name] = 0;
          }
          summary[v.varian_name] += v.total_qty;
        });
      });

      // Ubah jadi array
      this.totalVarianSummary = Object.entries(summary).map(([varian_name, total_qty]) => ({
        varian_name,
        total_qty
      }));
    },
    async fetchPaketDetailData() {
      try {
        const [forecastRes, variantRes] = await Promise.all([
          fetch("http://127.0.0.1:8000/api/detail-forecast"),
          fetch("http://127.0.0.1:8000/api/variant-result"),
        ]);

        const forecastJson = await forecastRes.json();
        const variantJson = await variantRes.json();

        const forecastMap = forecastJson.data;
        const variantMap = variantJson.data;

        const combinedData = forecastMap.map(pkg => {
          const match = variantMap.find(v => v.item_name === pkg.item_name);

          const calculatedVarians = (match?.varians || []).map(v => {
            const average = pkg.qty > 0 ? v.qty / pkg.qty : 0;
            const average_qty = Math.round(average); // Tanpa koma

            return {
              varian_name: v.varian_name,
              original_qty: v.qty,
              paket_qty: pkg.qty,
              average_qty: average_qty,
              total_qty: v.total_qty, // langsung dari API
            };
          });


          return {
            item_name: pkg.item_name,
            qty: pkg.qty,
            varians: calculatedVarians,
          };
        });

        this.paketDetailData = combinedData;
      } catch (error) {
        console.error("Gagal mengambil data gabungan paket:", error);
      }
    },
    fetchForecastData() {
      fetch("http://127.0.0.1:8000/api/detail-forecast")
        .then((res) => res.json())
        .then((json) => {
          if (json.status === "success") {
            this.forecastData = json.data;

            const forecastId = this.forecastData[0]?.id_forecast;
            if (forecastId) {
              this.fetchVarianData(forecastId);
            } else {
              console.warn("forecastId tidak ditemukan di forecastData");
            }
          } else {
            // Jika status bukan success
            this.showForecastError();
          }
        })
        .catch((err) => {
          console.error("Error fetching forecast data:", err);
          this.showForecastError();
        });
    },
    async fetchVarianData() {
      try {
        const response = await fetch(`http://127.0.0.1:8000/api/varian-result`);
        const data = await response.json();
        this.varianData = data.data; // karena API-mu return di dalam { status, data }
      } catch (error) {
        console.error("Gagal mengambil data varian:", error);
      }
    },
  }
};
</script>

<style>
html,
body,
#app {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
}

.table-scroll {
  overflow-x: auto;
  width: 100%;
}


.app-container {
  display: flex;
  height: 240vw;
  width: 98.8vw;
  background-color: #F4F4F4;
  /* untuk cegah scroll horizontal kalau ada */
}

.main-content {

  flex: 1;
  display: flex;
  flex-direction: column;
  width: 100%;
  box-sizing: border-box;
  background-color: #F3F5FB;
  /* Jaga-jaga kalau warna gelap berasal dari konten di sini */
}

#app {
  margin: 0;
  padding: 0;
}

/* Paket Irit */
.dashboard-content {
  padding: 20px;
  margin-left: 50vh;
  display: flex;
  flex-direction: column;
  gap: 20px;
  /* jarak antar‐elemen */
  /* Hapus margin‑left yang sebelumnya menggeser semuanya */
}

/* —— judul —— */
.dashboard-title h1 {
  margin-top: 60px;
  font-size: 24px;
  font-weight: 900;
  color: black;
  display: inline-block;
}

.dashboard-title .subtitle {
  font-size: 16px;
  color: #ADADAD;
  margin: 6px 0 0;
}

.chart-row {
  display: flex;
  flex-direction: row;
  gap: 20px;
  /* jarak antar chart */
  flex-wrap: wrap;
  /* agar responsif, chart turun ke bawah jika sempit */
}

/* ========== ORDER TABLE ========== */
/* .order-table {
  margin-top: 20px;
  margin-left: 312px;
  width: 31.7%;
  border-collapse: collapse;
  font-size: 15px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.07);
} */

.order-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 1rem;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.07);
}


.order-table th,
.order-table td {
  border: 1px solid #ddd;
  padding: 14px 16px;
  text-align: left;
  background-color: #ffffff;
  color: #444;
}

.order-table th {
  background-color: #F3F5FB;
  font-weight: 700;
}

.order-table tbody tr:hover {
  background-color: #f9f9f9;
  align-items: center;
}

@media (max-width: 768px) {
  .order-table {
    font-size: 0.9rem;
  }
}

@media (max-width: 480px) {
  .order-table {
    font-size: 0.8rem;
  }
}

.tables-row {
  margin-left: 295px;
  display: flex;
  gap: 50px;
  justify-content: center;
  margin-top: 20px;
  flex-wrap: wrap;
  /* agar responsif di layar kecil */
}

.table-title {
  font-size: 16px;
  font-weight: bold;
  color: #333;
  background-color: white;
  border: 1px solid #ddd;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.07);
  border-radius: 6px 6px 0 0;
  padding: 5px 6px 0 40%;
}

.table-title2 {
  font-size: 16px;
  font-weight: bold;
  color: #333;
  background-color: white;
  border: 1px solid #ddd;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.07);
  border-radius: 6px 6px 0 0;
  padding: 5px 6px 0 32%;
}

.table-title3 {
  font-size: 16px;
  font-weight: bold;
  color: #333;
  background-color: white;
  border: 1px solid #ddd;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.07);
  border-radius: 6px 6px 0 0;
  padding: 5px 6px 0 41%;
}

.table-wrapper {
  width: 100%;
  max-width: 100%;
  box-sizing: border-box;
}
.order-table td ul {
  list-style-type: disc;
  padding-left: 20px;
  margin: 0;
}
</style>
