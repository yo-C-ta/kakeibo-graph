<template>
    <div>
        <span>カテゴリー毎の支出額</span>
        <LineChart :chart-data="chartData" :xlbl="xlabel" :ylbl="ylabel"></LineChart>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Watch, Vue } from 'vue-property-decorator';
import LineChart from '@/components/charts/LineChart.vue';
import Chart from 'chart.js';

interface Data {
    year: string;
    month: string;
    category: string;
    spending: number;
    where: string;
    who: string;
}

@Component({
    components: {
        LineChart,
    },
})
export default class CategoryChart extends Vue {
    @Prop() public target!: string[];
    @Prop() public years!: string[];
    @Prop() public data!: Data[];

    public chartData: Chart.ChartData = {};
    private xlabel: string = 'カテゴリー';
    private ylabel: string = '金額';

    private created() {
        this.updateChart();
    }

    @Watch('target')
    private updtYears() {
        this.updateChart();
    }

    private updateChart() {
        const categoryChartData = this.categoryData();
        const palette = require('google-palette');
        const colors = palette('mpn65', this.years.length).map((hex: number) => {
            return '#' + hex;
        });
        let lbls: string[] = [];
        Object.values(categoryChartData).forEach((x: { [c: string]: number }) => {
            lbls = lbls.concat(Object.keys(x));
        });
        lbls = Array.from(new Set(lbls));
        this.chartData = {
            labels: lbls,
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
                    lineTension: 0.1,
                    hidden: !this.target.includes(x),
                };
            }),
        };
    }

    private categoryData() {
        const ctgrySpending: {
            [y: string]: { [c: string]: number },
        } = {};
        this.data.forEach((x: Data) => {
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
