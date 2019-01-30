<template>
  <div class="home">
    <MonthlyChart :data="jsonData" :years="years"></MonthlyChart>
    <CategoryChart :data="jsonData" :years="years"></CategoryChart>
    <MonthlyCategoryChart :data="jsonData" :years="years" :category="oneCategory"></MonthlyCategoryChart>
    <CategoryRateChart :data="jsonData" :years="years"></CategoryRateChart>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import jsonFile from '@/assets/data/Kakeibo.json';
import MonthlyChart from '@/components/MonthlyChart.vue';
import CategoryChart from '@/components/CategoryChart.vue';
import MonthlyCategoryChart from '@/components/MonthlyCategoryChart.vue';
import CategoryRateChart from '@/components/CategoryRateChart.vue';

@Component({
  components: {
    MonthlyChart,
    CategoryChart,
    MonthlyCategoryChart,
    CategoryRateChart,
  },
})
export default class Home extends Vue {
  private oneCategory: string = 'ガス代';
  private years: string[] = [];
  private jsonData: Array<{
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
  }> = jsonFile.kakeibo.map((x: any) => {
    const dt = new Date(Date.parse(x.date));
    const yr = dt.getFullYear().toString();
    if (this.years.indexOf(yr) === -1) {
      this.years.push(yr);
    }
    return {
      year: yr,
      month: (dt.getMonth() + 1).toString(),
      category: x.category,
      spending: x.spending,
      where: x.where,
      who: x.who,
    };
  });
}
</script>
