<script lang="ts">
import { Component, Prop, Mixins } from 'vue-property-decorator';
import { Line, mixins } from 'vue-chartjs';

@Component({})
export default class LineChart extends Mixins(Line, mixins.reactiveProp) {
  @Prop() public chartData: any;
  @Prop() public chartOptions: any;

  public mounted() {
    for (const dataset of this.chartData.datasets) {
      dataset.type = 'line';
      dataset.lineTension = 0;
    }
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
