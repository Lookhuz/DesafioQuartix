<template>
    <div class="challenge-container">
        <canvas id="myChart" width="1000" height="500"></canvas>
    </div>
</template>

<script lang="ts">
import Chart from 'chart.js/auto';
import axios from 'axios';

var values = [];
var dates = [];
let myChart;

export default {
    name: 'DesafioComponent',
    mounted() {
        this.getUsers();
        const data = {
            labels: dates,
            datasets: [{
                label: 'Daily Prices - High - 000002.SHZ',
                data: values,
                backgroundColor: 'rgb(255, 99, 132)',
                borderColor: 'rgb(255, 99, 132)',
            }]
        };
        const config = {
            type: 'line',
            data: data,
            options: {}
        };

        myChart = new Chart(
            document.getElementById('myChart'),
            config
        );
    },
    methods: {
        getUsers() {
            const response = axios.get('https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=000002.SHZ&outputsize=full&apikey=demo').then((result) => {
                if (result.status == 200) {
                    let i = 0;
                    for (const [date, value] of Object.entries(result.data['Time Series (Daily)'])) {
                        dates.push(date);
                        values.push(value["2. high"]);
                        i++;
                        if (i > 100) {
                            break;
                        }
                    }
                    myChart.update();
                    console.log('deu fetch', values);
                    console.log('dates', dates)
                    //this.dates = dates;
                } else {
                    console.error("Ocorreu um erro na API!");
                }
            })
        }
    },
    components: {
    }
}
</script>

<style scoped>

.challenge-container {
    margin: 0;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

#myChart {
    max-width: 1000px;
    max-height: 500px;
}
</style>