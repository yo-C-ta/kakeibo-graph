<script lang="ts">
import { Component, Prop, Watch, Mixins } from 'vue-property-decorator';
import { Bar, mixins } from 'vue-chartjs';
import Chart from 'chart.js';

@Component({})
export default class BarChart extends Mixins(Bar, mixins.reactiveProp) {
  @Prop() public chartData!: Chart.ChartData;
  @Prop() public chartOptions!: Chart.ChartOptions;

  public mounted() {
    if (this.chartData.datasets !== undefined) {
      for (const dataset of this.chartData.datasets) {
        dataset.type = 'bar';
      }
    }
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
