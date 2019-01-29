<script lang="ts">
import { Component, Prop, Mixins } from 'vue-property-decorator';
import { Pie, mixins } from 'vue-chartjs';

@Component({})
export default class PieChart extends Mixins(Pie, mixins.reactiveProp) {
  @Prop() public chartData: any;
  @Prop() public chartOptions: any;

  public mounted() {
    for (const dataset of this.chartData.datasets) {
      dataset.type = 'pie';
    }
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
