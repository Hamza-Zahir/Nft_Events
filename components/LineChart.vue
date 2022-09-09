
<script>
  import { Line } from "vue-chartjs";
  export default {
    extends: Line,
    props: {
      chartdata: {
        required: true,
        type: Object,
        default: () => {}
      },
      options: {
        required: true,
        type: Object,
      },
    },
    mounted() {

      this.renderChart(this.chartdata, this.options);
    },
  };
  </script>
