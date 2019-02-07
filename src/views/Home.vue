<template>
  <div class="home">
    <div class="columns">
      <div class="column">
        <div class="box">
          <MonthlyChart :data="jsonData" :years="targetYears"></MonthlyChart>
        </div>
        <div class="box">
          <CategoryChart :data="jsonData" :years="targetYears"></CategoryChart>
        </div>
      </div>

      <div class="column">
        <div class="box">
          <CategoryRateChart :data="jsonData" :years="targetYears"></CategoryRateChart>
        </div>
        <div class="box">
          <MonthlyCategoryChart :data="jsonData" :years="targetYears" :categorys="categorys"></MonthlyCategoryChart>
        </div>
      </div>
    </div>

    <b-field grouped group-multiline position="is-centered">
      <b-checkbox-button
        v-for="year in years"
        :key="year"
        v-model="targetYears"
        :native-value="year"
        size="is-small"
      >{{year}}</b-checkbox-button>
    </b-field>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import jsonFile from '@/assets/data/Kakeibo.json';
import MonthlyChart from '@/components/MonthlyChart.vue';
import CategoryChart from '@/components/CategoryChart.vue';
import MonthlyCategoryChart from '@/components/MonthlyCategoryChart.vue';
import CategoryRateChart from '@/components/CategoryRateChart.vue';

import Buefy from 'buefy';
import 'buefy/dist/buefy.css';

Vue.use(Buefy);

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
