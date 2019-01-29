<template>
  <div class="home">
    <LineChart :chart-data="monthlyChartData" :chart-options="monthlyChartOptions"></LineChart>
    <!-- <BarChart :chart-data="chartData" :chart-options="chartOptions"></BarChart> -->
    <PieChart :chart-data="categoryChartData" :chart-options="categoryChartOptions"></PieChart>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import jsonFile from '@/assets/data/Kakeibo.json';
import LineChart from '@/components/charts/LineChart.vue';
import BarChart from '@/components/charts/BarChart.vue';
import PieChart from '@/components/charts/PieChart.vue';

@Component({
  components: {
    LineChart,
    BarChart,
    PieChart,
  },
})
export default class Home extends Vue {
  // グラフ描画用データ
  public monthlyChartData: any;
  public categoryChartData: any;
  // グラフオプション
  public monthlyChartOptions = {
    title: {
      display: true,
      text: '家計簿',
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
          labelString: 'Month',
        },
      }],
      yAxes: [{
        stacked: true,
        scaleLabel: {
          display: true,
          labelString: 'Value',
        },
      }],
    },
  };
  public categoryChartOptions = {
    title: {
      display: true,
      text: '家計簿',
    },
    tooltips: {
      mode: 'index',
    },
    hover: {
      mode: 'index',
    },
  };


  private jsonData: Array<{
    date: Date,
    category: string,
    spending: number,
    where: string,
    who: string,
  }> = jsonFile.kakeibo.map((x: any) => {
    return {
      date: new Date(Date.parse(x.date)),
      category: x.category,
      spending: x.spending,
      where: x.where,
      who: x.who,
    };
  });
  private yearLables!: string[];

  private created() {
    const dateSpendingData = this.monthlyData();
    this.yearLables = Object.keys(dateSpendingData);
    this.monthlyChartData = this.monthlyChart(dateSpendingData, this.yearLables);

    const ctgrySpendingData = this.categoryData(this.yearLables);
    this.categoryChartData = this.ctgryChart(ctgrySpendingData);
  }

  private monthlyData() {
    const dateSpending: any = {};
    this.jsonData.forEach((x: any) => {
      const year = x.date.getFullYear();
      const month = x.date.getMonth() + 1;

      if (!(year in dateSpending)) {
        dateSpending[year] = {};
        for (const i of [...Array(12).keys()]) {
          dateSpending[year][i + 1] = 0;
        }
      }
      dateSpending[year][month] += x.spending;
    });
    return dateSpending;
  }

  private monthlyChart(dateSpendingData: any, years: string[]) {
    const palette = require('google-palette');
    const colors = palette('mpn65', years.length).map((hex: number) => {
      return '#' + hex;
    });
    return {
      // ラベル
      labels: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'],
      // データ詳細
      datasets: years.map((x: string, idx: number) => {
        return {
          label: x,
          data: Object.values(dateSpendingData[x]),
          backgroundColor: colors[idx],
        };
      }),
    };
  }

  private categoryData(years: string[]) {
    const ctgrySpending: any = {};
    this.jsonData.forEach((x: any) => {
      const year = x.date.getFullYear().toString();
      const ctgry = x.category;

      if (years.indexOf(year) >= 0) {
        if (!(ctgry in ctgrySpending)) {
          ctgrySpending[ctgry] = 0;
        }
        ctgrySpending[ctgry] += x.spending;
      }
    });

    return ctgrySpending;
  }

  private ctgryChart(ctgrySpendingData: any) {
    const palette = require('google-palette');
    const colors = palette('mpn65', Object.keys(ctgrySpendingData).length).map((hex: number) => {
      return '#' + hex;
    });

    return {
      // ラベル
      labels: Object.keys(ctgrySpendingData),
      // データ詳細
      datasets: [{
        data: Object.values(ctgrySpendingData),
        backgroundColor: colors,
      }],
    };
  }

}
</script>
