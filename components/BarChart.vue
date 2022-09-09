
<script>
import { Bar } from "vue-chartjs";
export default {
  extends: Bar,

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
