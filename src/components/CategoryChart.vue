<template>
  <div>
    <span>カテゴリー毎の支出額</span>
    <LineChart :chart-data="chartData" :chart-options="chartOptions"></LineChart>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator';
import LineChart from '@/components/charts/LineChart.vue';
import Chart from 'chart.js';

@Component({
  components: {
    LineChart,
  },
})
export default class CategoryChart extends Vue {
  @Prop() public years!: string[];
  @Prop() public data!: Array<{
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
  }>;

  public chartData: Chart.ChartData = {};
  public chartOptions: Chart.ChartOptions = {
    responsive: true,
    title: {
      display: false,
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
          labelString: 'カテゴリー',
        },
        ticks: {
          autoSkip: false,
        },
      }],
      yAxes: [{
        // stacked: true,
        scaleLabel: {
          display: true,
          labelString: '金額',
        },
      }],
    },
  };

  private created() {
    this.updateChart();
  }

  @Watch('years')
  private updtYears() {
    this.updateChart();
  }

  private updateChart() {
    const categoryChartData = this.categoryData();
    const palette = require('google-palette');
    const colors = palette('mpn65', this.years.length).map((hex: number) => {
      return '#' + hex;
    });
    let lbls: string[] = [];
    Object.values(categoryChartData).forEach((x: any) => {
      lbls = lbls.concat(Object.keys(x));
    });
    lbls = Array.from(new Set(lbls));
    this.chartData = {
      labels: lbls,
      datasets: this.years.map((x: string, idx: number) => {
        const arr: number[] = [];
        lbls.forEach((y: string) => {
          if (categoryChartData[x][y]) {
            arr.push(categoryChartData[x][y]);
          } else {
            arr.push(0);
          }
        });
        return {
          label: x,
          data: arr,
          backgroundColor: 'rgba(0,0,0,0)',
          borderColor: colors[idx],
          lineTension: 0.1,
        };
      }),
    };
  }

  private categoryData() {
    const ctgrySpending: any = {};
    this.data.forEach((x: any) => {
      if (!(x.year in ctgrySpending)) {
        ctgrySpending[x.year] = {};
      }
      if (!(x.category in ctgrySpending[x.year])) {
        ctgrySpending[x.year][x.category] = 0;
      }
      ctgrySpending[x.year][x.category] += x.spending;
    });
    return ctgrySpending;
  }
}
</script>
