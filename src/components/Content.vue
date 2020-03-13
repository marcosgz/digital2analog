<template>
  <div class="chart">
    <p><strong>Entrada:</strong> <input type="text" v-model="text" /></p>
    <p><strong>Caracteres:</strong> {{ text2Chars(text) }}</p>
    <p><strong>ASCII:</strong> {{ text2ASCII(text) }}</p>
    <p><strong>Binário:</strong> {{ text2Binary(text) }}</p>
    <section>
      <BarChart
        :chart-data="binaryChartData"
        :options="binaryChartOptions"
        class="chart"
       />
    </section>
    <section>
      <LineChart
        :chart-data="askChartData"
        :options="lineChartOptions"
        class="chart"
      />
    </section>
    <section>
      <LineChart
        :chart-data="fskChartData"
        :options="lineChartOptions"
        class="chart"
      />
    </section>
    <section>
      <LineChart
        :chart-data="pskChartData"
        :options="lineChartOptions"
        class="chart"
      />
    </section>
  </div>
</template>

<script>

import LineChart from './LineChart.vue'
import BarChart from './BarChart.vue'

export default {
  name: 'Content',

  components: {
    LineChart,
    BarChart
  },

  data() {
    let lineChartOpts = {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        yAxes: [{
          ticks: {
            min: -2,
            max: 2,
            precision: 0,
            beginAtZero: false,
          }
        }],
        xAxes: [{
          afterBuildTicks(scale, ticks) {
            if (ticks.length < 2) { return []; }

            ticks[0] = ticks[0].replace('/>', '')
            ticks[ticks.length - 1] = '/>';
            return ticks;
          },
          ticks: {
            maxRotation: 45,
            minRotation: 0,
            stepSize: 1,
            fontSize: 9,
            callback: function(value) {
              if (value === null) { return null }
              return value;
            }
          }
        }]
      },
    };

    let pskChartOpts = lineChartOpts;

    return {
      text: 'xyz',
      color: '#f87979',
      binaryChartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          yAxes: [{
            ticks: {
              min: 0,
              max: 2,
              precision: 0,
              beginAtZero: true,
            }
          }],
        },
      },
      lineChartOptions: lineChartOpts,
      pskChartOptions: pskChartOpts,
    }
  },

  computed: {
    binaries() {
      return this.text2Binary(this.text).replace(' ', '').split('');
    },
    binaryChartData() {
      let arr = this.binaries;
      let h = {
        labels: arr,
        datasets: [
          {
            label: 'Binary Signal',
            backgroundColor: this.color,
            barPercentage: 1,
            categoryPercentage: 1,
            data: arr.map((v) => { return parseInt(v) == 0 ? 0 : 1 })
          }
        ]
      }
      return h;
    },
    askChartData() {
      let h = {
        labels: this.binaries.flatMap(v => {
          return ['/> <' + v, null, null, null];
        }),
        datasets: [
          {
            label: 'ASK - Amplitude Modulation',
            borderColor: this.color,
            fill: false,
            pointRadius: 0,
            pointBorderColor: 'transparent',
            lineTension: 0.6,
            data: this.binaries.flatMap(b => {
              if (parseInt(b) === 0) {
                return [0, 0, 0, 0];
              } else {
                return [1, -1, 1, -1];
              }
            }),
          }
        ]
      }
      return h;
    },
    fskChartData() {
      let h = {
        labels: this.binaries.flatMap(v => {
          return ['/> <' + v, null, null, null];
        }),
        datasets: [
          {
            label: 'FSK - Frequency Modulation',
            borderColor: this.color,
            fill: false,
            pointRadius: 0,
            pointBorderColor: 'transparent',
            lineTension: 0.6,
            data: this.binaries.flatMap(b => {
              if (parseInt(b) === 0) {
                return [1, 0.3, -0.3, -1];
              } else {
                return [1, -1, 1, -1];
              }
            }),
          }
        ]
      }
      return h;
    },
    pskChartData() {
      let last = null;
      let labels = []
      this.binaries.forEach((v) => {
        if (last === null || last === v) {
          last = v;
          labels.push('/> <' + v, null);
        } else {
          last = v;
          labels.push('/> <' + v, null, null);
        }
      })

      last = null; // Redefine last
      let data = []
      this.binaries.forEach(v => {
        let b = parseInt(v) === 0 ? -1 : 1;

        if (last === null || last === b) {
          last = b;
          data.push(b, -b);
        } else {
          last = b;
          data.push(0, b, -b);
        }
      });

      let h = {
        labels: labels,
        datasets: [
          {
            label: 'PSK - Phase Modulation',
            borderColor: this.color,
            fill: false,
            pointRadius: 0,
            pointBorderColor: 'transparent',
            lineTension: 0.6,
            data: data,
          }
        ]
      }
      return h;
    },
  },

  methods: {
    text2Binary(string) {
      return string.split('').map((char) => {
        return char.charCodeAt(0).toString(2);
      }).join(' ');
    },
    text2ASCII(string) {
     return string.split('').map((char) => {
        return char.charCodeAt(0);
      }).join(' ');
    },
    text2Chars(string) {
      return string.split('').join(' ');
    }
  }
}
</script>

<style scoped>
strong {
  display: inline-block;
  min-width: 100px;
  text-align: right;
  padding-right: 10px;
}
input[type=text] {
  padding: 5px 8px;
  width: 50px;
}
section {
  background-color: #fff;
  margin-bottom: 45px;
  padding: 25px 10px;
  position: relative;
}
section > .chart {
  height: 200px;
}
</style>
