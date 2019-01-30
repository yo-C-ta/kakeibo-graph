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
export default class CategoryChart extends Vue {
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
      text: 'カテゴリー毎の支出額',
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
      // ラベル
      labels: lbls,
      // データ詳細
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
