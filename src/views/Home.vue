<template>
  <div class="home">
    <p v-for="year in years" :key="year">
      <input type="checkbox" :id="year" v-model="targetYears" v-bind:value="year">
      <label :for="year">{{ year }}</label>
    </p>
    <select name="category" v-model="targetCtgry">
      <option v-for="category in categorys" :value="category" :key="category">{{ category }}</option>
    </select>
    <MonthlyChart :data="jsonData" :years="targetYears"></MonthlyChart>
    <CategoryChart :data="jsonData" :years="targetYears"></CategoryChart>
    <MonthlyCategoryChart :data="jsonData" :years="targetYears" :category="targetCtgry"></MonthlyCategoryChart>
    <CategoryRateChart :data="jsonData" :years="targetYears"></CategoryRateChart>
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
  private targetYears: string[] = [
    (new Date().getFullYear() - 1).toString(),
    (new Date().getFullYear()).toString(),
  ];
  private targetCtgry: string = '食費';

  private years: string[] = [];
  private categorys: string[] = [];
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
    if (this.categorys.indexOf(x.category) === -1) {
      this.categorys.push(x.category);
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
