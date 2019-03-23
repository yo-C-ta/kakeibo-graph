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
    cutoutPercentage: 40,
  };

  public mounted() {
    if (this.chartData.datasets !== undefined) {
      for (const dataset of this.chartData.datasets) {
        dataset.type = 'pie';
      }
    }
    this.addPlugin({
      // tslint:disable-next-line
      afterDraw: (chart: any, easing: number) => {
        let dataSum = 0;
        chart.data.datasets[0].data.forEach((elem: any) => {
          dataSum += elem;
        });
        chart.data.datasets.forEach((dataset: any, i: number) => {
          const meta = chart.getDatasetMeta(i);
          if (!meta.hidden) {
            meta.data.forEach((element: any, index: number) => {
              const dataString = (Math.round(dataset.data[index] / dataSum * 1000) / 10).toString() + '%';
              const position = element.tooltipPosition();

              chart.ctx.fillStyle = 'white';
              chart.ctx.font = Chart.helpers.fontString(12, 'normal', 'Helvetica Neue');
              chart.ctx.textAlign = 'center';
              chart.ctx.textBaseline = 'middle';

              if (dataString !== '0%') {
                chart.ctx.fillText(dataString, position.x, position.y);
              }
            });
          }
        });
        chart.ctx.fillStyle = 'black';
        const centerX = ((chart.chartArea.left + chart.chartArea.right) / 2);
        const centerY = ((chart.chartArea.top + chart.chartArea.bottom) / 2);
        chart.ctx.font = Chart.helpers.fontString(18, 'normal', 'Helvetica Neue');
        chart.ctx.textAlign = 'center';
        chart.ctx.textBaseline = 'middle';
        chart.ctx.fillText('¥' + dataSum.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ','), centerX, centerY);
      },
    });
    this.renderChart(this.chartData, this.chartOptions);
  }
}
</script>
