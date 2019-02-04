<template>
  <div>各支出カテゴリーの占める割合
    <PieChart :chart-data="chartData" :chart-options="chartOptions"></PieChart>
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
  private chartOptions: Chart.ChartOptions = {};

  private created() {
    this.updateChart();
  }

  @Watch('years')
  private updtYears() {
    this.updateChart();
  }

  private updateChart() {
    const ctgrySpendingData = this.categoryData(this.years);
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
    this.chartOptions = {
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
  }

  private categoryData(years: string[]) {
    const ctgrySpending: { [key: string]: number } = {};
    this.data.forEach((x: any) => {
      if (this.years.indexOf(x.year) >= 0) {
        if (!(x.category in ctgrySpending)) {
          ctgrySpending[x.category] = 0;
        }
        ctgrySpending[x.category] += x.spending;
      }
    });
    return ctgrySpending;
  }
}
</script>
