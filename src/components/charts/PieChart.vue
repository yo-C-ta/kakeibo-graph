<script lang="ts">
import { Component, Prop, Watch, Mixins } from 'vue-property-decorator';
import { Pie, mixins } from 'vue-chartjs';
import Chart from 'chart.js';

@Component({})
export default class PieChart extends Mixins(Pie, mixins.reactiveProp) {
  @Prop() public chartData!: Chart.ChartData;
  public chartOptions: Chart.ChartOptions = {
    title: {
      display: false,
    },
    tooltips: {
      mode: 'index',
    },
    hover: {
      mode: 'index',
    },
  };

  public mounted() {
    if (this.chartData.datasets !== undefined) {
      for (const dataset of this.chartData.datasets) {
        dataset.type = 'pie';
      }
    }
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
