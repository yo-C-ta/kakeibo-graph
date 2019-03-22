<script lang="ts">
import { Component, Prop, Watch, Mixins } from 'vue-property-decorator';
import Chart from 'chart.js';
import { Line, mixins } from 'vue-chartjs';

@Component({})
export default class LineChart extends Mixins(Line, mixins.reactiveProp) {
  @Prop() public chartData!: Chart.ChartData;
  @Prop() public xlbl!: string;
  @Prop() public ylbl!: string;
  public chartOptions: Chart.ChartOptions = {
    responsive: true,
    title: {
      display: false,
    },
    legend: {
      onClick: (e) => e.stopPropagation(),
    },
    tooltips: {
      mode: 'index',
    },
    hover: {
      mode: 'index',
    },
    scales: {
      xAxes: [{
        scaleLabel: {
          display: true,
          labelString: this.xlbl,
        },
      }],
      yAxes: [{
        scaleLabel: {
          display: true,
          labelString: this.ylbl,
        },
      }],
    },
  };

  public mounted() {
    if (this.chartData.datasets !== undefined) {
      for (const dataset of this.chartData.datasets) {
        dataset.type = 'line';
      }
    }
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
