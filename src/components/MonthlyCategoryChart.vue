<template>
  <div>
    <b-field grouped position="is-centered">
      <span>月ごとの</span>
      <b-select name="category" v-model="category" size="is-small">
        <option v-for="category in categorys" :value="category" :key="category">{{ category }}</option>
      </b-select>
      <span>の支出額</span>
    </b-field>
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
  @Prop() public categorys!: string[];
  @Prop() public data!: Array<{
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
  }>;

  private category: string = '食費';
  private chartData: Chart.ChartData = {};
  private chartOptions: Chart.ChartOptions = {};

  private created() {
    this.updateChart();
  }

  @Watch('years')
  private updtYears() {
    this.updateChart();
  }
  @Watch('category')
  private updtCtgry() {
    this.updateChart();
  }

  private updateChart() {
    const monthCtgryData = this.monthlyData();
    const palette = require('google-palette');
    const colors = palette('mpn65', this.years.length).map((hex: number) => {
      return '#' + hex;
    });
    this.chartData = {
      labels: [...Array(12).keys()].map((x: number) => ((x + 1).toString() + '月')),
      datasets: this.years.map((x: string, idx: number) => {
        return {
          label: x,
          data: monthCtgryData[x] ? Object.values(monthCtgryData[x]) : [],
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
      if (x.category === this.category) {
        if (!(x.year in dateSpending)) {
          dateSpending[x.year] = {};
          for (const i of [...Array(12).keys()]) {
            dateSpending[x.year][(i + 1).toString()] = 0;
          }
        }
        dateSpending[x.year][x.month] += x.spending;
      }
    });
    return dateSpending;
  }
}
</script>
