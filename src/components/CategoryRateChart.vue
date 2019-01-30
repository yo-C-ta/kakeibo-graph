<template>
  <div>
    <PieChart :chart-data="chartData" :chart-options="chartOptions"></PieChart>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import PieChart from '@/components/charts/PieChart.vue';

@Component({
  components: {
    PieChart,
  },
})
export default class CategoryRateChart extends Vue {
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
      text: '各支出カテゴリーの占める割合',
    },
    tooltips: {
      mode: 'index',
    },
    hover: {
      mode: 'index',
    },
  };

  private created() {
    const ctgrySpendingData = this.categoryData(this.years);
    const palette = require('google-palette');
    const colors = palette('mpn65', Object.keys(ctgrySpendingData).length).map((hex: number) => {
      return '#' + hex;
    });

    this.chartData = {
      // ラベル
      labels: Object.keys(ctgrySpendingData),
      // データ詳細
      datasets: [{
        data: Object.values(ctgrySpendingData),
        backgroundColor: colors,
      }],
    };
  }

  private categoryData(years: string[]) {
    const ctgrySpending: any = {};
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
