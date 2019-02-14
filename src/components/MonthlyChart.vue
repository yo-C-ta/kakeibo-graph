<template>
  <div>
    <span>月毎の支出額</span>
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
export default class MonthlyChart extends Vue {
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
    const palette = require('google-palette');
    const colors = palette('mpn65', this.years.length).map((hex: number) => {
      return '#' + hex;
    });
    const mnthly = this.monthlyData();
    this.chartData = {
      labels: [...Array(12).keys()].map((x: number) => (x.toString() + '月')),
      datasets: this.years.map((x: string, idx: number) => {
        return {
          label: x,
          data: Object.keys(mnthly).length !== 0 ? Object.values(mnthly[x]) : [],
          backgroundColor: 'rgba(0,0,0,0)',
          borderColor: colors[idx],
          lineTension: 0.1,
        };
      }),
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
      scales: {
        xAxes: [{
          scaleLabel: {
            display: true,
            labelString: '月',
          },
        }],
        yAxes: [{
          scaleLabel: {
            display: true,
            labelString: '金額',
          },
        }],
      },
    };
  }

  private monthlyData() {
    const dateSpending: any = {};
    this.data.forEach((x: any) => {
      if (!(x.year in dateSpending)) {
        dateSpending[x.year] = {};
        for (const i of [...Array(12).keys()]) {
          dateSpending[x.year][(i + 1).toString()] = 0;
        }
      }
      dateSpending[x.year][x.month] += x.spending;
    });
    return dateSpending;
  }
}
</script>
