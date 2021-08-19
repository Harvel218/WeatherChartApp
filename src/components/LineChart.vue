<script>
import { Line } from "vue-chartjs";
export default {
  extends: Line,
  props: {
    chartData: {
      type: Array,
      required: true,
    },
    chartLabels: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      options: {
        title: {
          display: true,
          text: "Temperature in next 12 hours",
        },
        scales: {
          y: {
            ticks: {
              // Include a dollar sign in the ticks
              callback: function (value) {
                return "$" + value;
              },
            },
          },
          yAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "Temperature [°C]",
              },
              ticks: {
                beginAtZero: true,
              },
              gridLines: {
                display: true,
              },
            },
          ],
          xAxes: [
            {
              scaleLabel: {
                display: true,
                labelString: "Time [h]",
              },
              gridLines: {
                display: true,
              },
            },
          ],
        },
        legend: {
          display: false,
        },
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  mounted() {
    this.renderChart(
      {
        labels: this.chartLabels,
        datasets: [
          {
            label: "",
            borderColor: "#27876f",
            pointBackgroundColor: "white",
            borderWidth: 4,
            pointBorderColor: "#007d5f",
            backgroundColor: "transparent",
            data: this.chartData,
          },
        ],
      },
      this.options
    );
  },
};
</script>