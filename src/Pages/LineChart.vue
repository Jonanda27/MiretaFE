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
      const sortedData = [...this.historyData].sort((a, b) => new Date(a.x) - new Date(b.x));
      const labels = sortedData.map(d => d.x);
      const lastIndex = sortedData.length - 1;

      const historyDataset = sortedData.map((d, i) => {
        if (i === lastIndex && (this.year === 2025 || this.year === 'All')) {
          return null; // exclude from blue line
        }
        return d.y;
      });

      const forecastDataset = sortedData.map((d, i) => {
        if (i === lastIndex && (this.year === 2025 || this.year === 'All')) {
          return d.y; // only show last point in yellow
        }
        return null;
      });

      const forecastLine = sortedData.map((d, i) =>
        (this.year === 2025 || this.year === 'All') && (i === lastIndex - 1 || i === lastIndex)
          ? d.y
          : null
      );

      return {
        labels,
        datasets: [
          {
            label: "Data History",
            data: historyDataset,
            borderColor: "rgba(102, 102, 255, 1)",
            backgroundColor: "rgba(102, 102, 255, 1)",
            pointBackgroundColor: "rgba(102, 102, 255, 1)",
            fill: false,
            tension: 0.3
          },
          {
            label: "Titik Forecast",
            data: forecastDataset,
            borderColor: "transparent",
            backgroundColor: "yellow",
            pointBackgroundColor: "yellow",
            pointRadius: 6,
            fill: false,
            showLine: false
          },
          {
            label: "Forecast Line",
            data: forecastLine,
            borderColor: "yellow",
            backgroundColor: "transparent",
            pointBackgroundColor: "transparent",
            borderDash: [5, 5],
            fill: false,
            tension: 0.3,
            pointRadius: 0,
            pointHoverRadius: 0,
            datalabels: {
              display: false
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
