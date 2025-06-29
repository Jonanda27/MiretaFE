<template>
  <div style="width: 100%; height: 100%;">
    <Line :data="processedChartData" :options="chartOptions" />
  </div>
</template>

<script>
import { Line } from 'vue-chartjs';
import ChartDataLabels from 'chartjs-plugin-datalabels';
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  PointElement,
  CategoryScale,
  LinearScale
} from 'chart.js';

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  PointElement,
  CategoryScale,
  LinearScale,
  ChartDataLabels
);

export default {
  name: 'LineChart',
  components: { Line },
  props: {
    historyData: {
      type: Array,
      required: true
    },
    year: {
      type: [String, Number],
      default: 'All'
    }
  },
  computed: {
    processedChartData() {
      const sorted = [...this.historyData].sort((a, b) => new Date(a.x) - new Date(b.x));
      const labels = sorted.map(d => d.x);

      const historyPoints = sorted.map(d => d.source === 'history' ? d.y : null);
      const forecastPoints = sorted.map(d => d.source === 'forecast' ? d.y : null);

      // Ambil titik terakhir dari history dan pertama dari forecast
      const lastHistory = [...sorted].reverse().find(d => d.source === 'history');
      const firstForecast = sorted.find(d => d.source === 'forecast');

      // Dataset untuk forecast line (dua titik)
      let forecastLine = labels.map(() => null);
      if (lastHistory && firstForecast) {
        const lastIdx = labels.indexOf(lastHistory.x);
        const firstIdx = labels.indexOf(firstForecast.x);
        if (lastIdx !== -1 && firstIdx !== -1) {
          forecastLine[lastIdx] = lastHistory.y;
          forecastLine[firstIdx] = firstForecast.y;
        }
      }

      return {
        labels,
        datasets: [
          {
            label: "Data History",
            data: historyPoints,
            borderColor: "rgba(102, 102, 255, 1)",
            backgroundColor: "rgba(102, 102, 255, 1)",
            pointBackgroundColor: "rgba(102, 102, 255, 1)",
            fill: false,
            tension: 0.3,
            spanGaps: true,
            datalabels: {
              display: true,
              color: '#000',
              align: 'top',
              anchor: 'end',
              font: {
                weight: 'bold',
                size: 10
              },
              formatter: value => value ?? ''
            }
          },
          {
            label: "Forecast",
            data: forecastPoints,
            borderColor: "yellow",
            backgroundColor: "yellow",
            pointBackgroundColor: "yellow",
            fill: false,
            tension: 0.3,
            spanGaps: true,
            datalabels: {
              display: true,
              color: '#000',
              align: 'top',
              anchor: 'end',
              font: {
                weight: 'bold',
                size: 10
              },
              formatter: value => value ?? ''
            }
          },
          {
            label: "Forecast Line",
            data: forecastLine,
            borderColor: "yellow",
            borderDash: [5, 5],
            fill: false,
            tension: 0.3,
            pointRadius: 0,
            pointHoverRadius: 0,
            backgroundColor: "transparent",
            pointBackgroundColor: "transparent",
            datalabels: {
              display: false // ⛔ jangan tampilkan label untuk garis forecast penghubung
            }
          }
        ]
      };

    }

  },
  data() {
    return {
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: true,
            position: 'bottom',
            labels: {
              font: { weight: 'bold' },
              color: '#000',
              usePointStyle: true,
              pointStyle: 'circle'
            }
          },
          tooltip: {
            enabled: true
          },
          datalabels: {
            display: false // ⛔ tidak menampilkan angka di atas titik
          }
        },
        scales: {
          y: {
            beginAtZero: true,
            title: {
              display: true,
              text: 'Jumlah'
            },
            ticks: {
              stepSize: 10
            }
          },
          x: {
            title: {
              display: true,
              text: 'Periode'
            }
          }
        }
      }
    };
  }
};
</script>
