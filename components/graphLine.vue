
<template>
  <div class="chart-div">
    <LineChart :chartdata="chartdata" :options="options" />
  </div>
</template>
<script>
  import LineChart from "./LineChart.vue"
export default {
  comments:{
    LineChart
  },
  props: {
    dates: {
      required: true,
      type: Array,
    },
    counts: {
      required: true,
      type: Array,
    },
  },
  data() {
    return {
      chartdata: {
        labels: this.dates,

        datasets: [
          {data: this.counts,

            borderColor: "#e0aefd",
            bodyFillBackground: "6392f7f6",
            fill: true,
            // backgroundColor: "#f87979",
            backgroundColor: (ctx, chartArea) => {
              const canvas = ctx.chart.ctx;
              const gradient = canvas.createLinearGradient(0, 0, 0, 360);

              gradient.addColorStop(0, "#37d0eb");
              gradient.addColorStop(0.5, "#37d0eba1");
              gradient.addColorStop(1, "#37d0eb67");

              return gradient;
            },
            label: "",
            pointRadius: 5,
            pointBackgroundColor: "transparent",
            pointBorderColor: "transparent",
          },

        ],
      },

      options: {
        legend: { display: false },
        maintainAspectRatio: false,
        responsive: true,
        tooltips: {
          backgroundColor: "#ffffff",
          titleFontColor: "#7c7c7c",
          bodyFontColor: "black",
          position: "nearest",
          mode: "nearest",
          intersect: 0,
          bodySpacing: 4,
          xPadding: 15,
          yPadding: 8,
          callbacks: {
            label: function (tooltipItems) {
              return ` $ ${tooltipItems.yLabel}`;
            },
          },
        },
        scales: {
          xAxes: [
            {
              gridLines: {
                display: false,
              },
              ticks: {
                callback: function (value, i, u) {
                  let _value = value.split("/")
                  return `${_value[2]} ${_value[1]}`;
                },
              },
            },
          ],
          yAxes: [
            {
              ticks: {
                callback: function (value) {
                  return `$ ${value}`;
                },
                beginAtZero: true,
              },
              gridLines: {
                display: false,
              },
            },
          ],
        },
      },
    };
  },
};
</script>
<style>
  body{
    /* background: #6392f7f6; */
  }
</style>
