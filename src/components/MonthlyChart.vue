<template>
  <div>
    <LineChart :chart-data="chartData" :chart-options="chartOptions"></LineChart>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import LineChart from '@/components/charts/LineChart.vue';

@Component({
  components: {
    LineChart,
  },
})
export default class MonthlyChart extends Vue {
  @Prop() public data!: Array<{
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
  }>;
  @Prop() public years!: string[];

  private chartData: any;
  private chartOptions = {
    title: {
      display: true,
      text: '月毎の支出額',
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
        // stacked: true,
        scaleLabel: {
          display: true,
          labelString: '金額',
        },
      }],
    },
  };

  private created() {
    const palette = require('google-palette');
    const colors = palette('mpn65', this.years.length).map((hex: number) => {
      return '#' + hex;
    });
    this.chartData = {
      // ラベル
      labels: [...Array(12).keys()].map((x: number) => (x.toString() + '月')),
      // データ詳細
      datasets: this.years.map((x: string, idx: number) => {
        return {
          label: x,
          data: Object.values(this.monthlyData()[x]),
          backgroundColor: 'rgba(0,0,0,0)',
          borderColor: colors[idx],
        };
      }),
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
