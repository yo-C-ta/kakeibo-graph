<template>
    <div>
        <b-field grouped position="is-centered">
            <span>月ごとの</span>
            <b-select name="category" v-model="category" size="is-small">
                <option
                    v-for="category in categorys"
                    :value="category"
                    :key="category"
                >{{ category }}</option>
            </b-select>
            <span>の支出額</span>
        </b-field>
        <LineChart :chart-data="chartData" :xlbl="xlabel" :ylbl="ylabel"></LineChart>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator';
import LineChart from '@/components/charts/LineChart.vue';
import Chart from 'chart.js';

type Data = {
    year: string,
    month: string,
    category: string,
    spending: number,
    where: string,
    who: string,
}

@Component({
    components: {
        LineChart,
    },
})
export default class MonthlyChart extends Vue {
    @Prop() public target!: string[];
    @Prop() public years!: string[];
    @Prop() public categorys!: string[];
    @Prop() public data!: Array<Data>;

    private category: string = '食費';
    private chartData: Chart.ChartData = {};
    private xlabel: string = '月';
    private ylabel: string = '金額';

    private created() {
        this.updateChart();
    }

    @Watch('target')
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
                    hidden: !this.target.includes(x),
                };
            }),
        };
    }

    private monthlyData() {
        const dateSpending: {
            [y: string]: { [m: string]: number }
        } = {};
        this.data.forEach((x: Data) => {
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
