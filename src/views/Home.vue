<template>
  <div class="home">
    <b-field grouped group-multiline position="is-centered">
      <b-checkbox-button
        v-for="year in years"
        :key="year"
        v-model="targetYears"
        :native-value="year"
      >{{year}}</b-checkbox-button>
    </b-field>

    <div v-if="targetYears.length !== 0">
      <div class="columns">
        <div class="column">
          <div class="box">
            <MonthlyChart :data="jsonData" :years="years" :target="targetYears"></MonthlyChart>
          </div>
          <div class="box">
            <CategoryChart :data="jsonData" :years="years" :target="targetYears"></CategoryChart>
          </div>
        </div>
        <div class="column">
          <div class="box">
            <CategoryRateChart :data="jsonData" :years="years" :target="targetYears"></CategoryRateChart>
          </div>
          <div class="box">
            <MonthlyCategoryChart
              :data="jsonData"
              :years="years"
              :target="targetYears"
              :categorys="categorys"
            ></MonthlyCategoryChart>
          </div>
        </div>
      </div>
    </div>
    <div v-else>No data to display</div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import confFile from '@/assets/data/Confidential.json';
import MonthlyChart from '@/components/MonthlyChart.vue';
import CategoryChart from '@/components/CategoryChart.vue';
import MonthlyCategoryChart from '@/components/MonthlyCategoryChart.vue';
import CategoryRateChart from '@/components/CategoryRateChart.vue';

import VueJsonp from 'vue-jsonp';
Vue.use(VueJsonp);

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
  private targetYears: string[] = [];
  private years: string[] = [];
  private categorys: string[] = [];
  private jsonData: Array<{
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
  }> = [];

  private created() {
    this.$jsonp(confFile.URL, {
      callbackName: confFile.CB,
    }).then((json) => {
      this.jsonData = json.kakeibo.map((x: any) => {
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
      this.targetYears.push(this.years[this.years.length - 1]);
    }).catch((err) => {
      /* */
    });
  }
}
</script>
