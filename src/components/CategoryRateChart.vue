<template>
  <div>
    <span>各支出カテゴリーの占める割合</span>
    <PieChart :chart-data="chartData" :plugins="[textInChart]"></PieChart>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator';
import PieChart from '@/components/charts/PieChart.vue';
import Chart from 'chart.js';

@Component({
  components: {
    PieChart,
  },
})
export default class CategoryRateChart extends Vue {
  @Prop() public target!: string[];
  @Prop() public years!: string[];
  @Prop() public data!: Array<{
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
  }>;

  private chartData: Chart.ChartData = {};
  private textInChart = {
    afterDraw: (chart: any, easing: string) => {
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
    }
  };

  private created() {
    this.updateChart();
  }

  @Watch('target')
  private updtYears() {
    this.updateChart();
  }

  private updateChart() {
    const ctgrySpendingData = this.categoryData(this.target);
    const palette = require('google-palette');
    const colors: string[] = palette('mpn65', Object.keys(ctgrySpendingData).length).map((hex: number) => {
      return '#' + hex;
    });
    this.chartData = {
      labels: Object.keys(ctgrySpendingData),
      datasets: [{
        data: Object.values(ctgrySpendingData),
        backgroundColor: colors,
      }],
    };
  }

  private categoryData(years: string[]) {
    const ctgrySpending: { [key: string]: number } = {};
    this.data.forEach((x: any) => {
      if (!(x.category in ctgrySpending)) {
        ctgrySpending[x.category] = 0;
      }
      if (years.indexOf(x.year) >= 0) {
        ctgrySpending[x.category] += x.spending;
      }
    });
    return ctgrySpending;
  }
}
</script>
