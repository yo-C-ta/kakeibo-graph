<script lang="ts">
import { Component, Prop, Mixins } from 'vue-property-decorator';
import { Bar, mixins } from 'vue-chartjs';

@Component({})
export default class BarChart extends Mixins(Bar, mixins.reactiveProp) {
  @Prop() public chartData: any;
  @Prop() public chartOptions: any;

  public mounted() {
    for (const dataset of this.chartData.datasets) {
      dataset.type = 'bar';
    }
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
