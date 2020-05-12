<template>
  <div style="height: 300px">
    <line-chart :chart-data="lineChartData"
    :height="300"
    :options="{ 
      responsive: true ,
      maintainAspectRatio: false,
    }"></line-chart>
  </div>
</template>


<script>
import 'chart.js';
import LineChart from './lineChart'
// import convert from './convert';


function getRandomColor() {
  const letters = '0123456789ABCDEF';
  let color = '#';
  for (let i = 0; i < 6; i++) {
    color += letters[Math.floor(Math.random() * 16)];
  }
  return color;
}


export default {
  props: ['cartData', 'heading', 'inTimeOrder'],
  data() {
    return {
      styles: {
        height: `300px`,
        position: 'relative',
        responsive: true
      }
    }
  },
  methods: {
    
  },
  components: {
    LineChart
  },
  computed: {
    lineChartData() {
      
      const datasets = this.cartData.map(item => {
        let data = [];


        for (const key in item) {
          if (key !== '名稱') {
            data.push(+item[key]);
          }
        }

        // 如果要反轉，就反轉資料集
        if (!this.inTimeOrder) {
          data = data.reverse();
        }

        return {
          label: item['名稱'],
          // fill: false,
          // backgroundColor: '#f87979',
          borderColor: getRandomColor(),
          fill: 'boundary',
          lineTension: 0,
          data
        }
      });
      // console.log('inTimeOrder', this.inTimeOrder)
      const lineChartData = {
        labels: this.inTimeOrder ?
          this.heading.filter(item => item !== '名稱'):
          this.heading.filter(item => item !== '名稱').reverse(),
        datasets
      }

      // console.log('lineChartData', lineChartData);
      return lineChartData;
    }
  },
  // watch: {
  //   lineChartData() {
  //     console.log(this.lineChartData);
  //     window.myLine.update()
  //   }
  // },
  mounted() {
    // const ctx = document.getElementById('canvas').getContext('2d');
    // window.myLine = Chart.Line(ctx, {
    //   data: {"labels":["2020Q1","2019Q4","2019Q3","2019Q2","2019Q1","2018Q4","2018Q3","2018Q2","2018Q1","2017Q4","2017Q3","2017Q2","2017Q1","2016Q4\r"],"datasets":[{"label":"公平價值衡量列入損益之金融資產–流動","fill":false,"yAxisID":"公平價值衡量列入損益之金融資產–流動","data":[0,0,0,0.85,0.85,0.81,0.87,1.32,2.18,0,0,0,0]},{"label":"公平價值衡量列入其他綜合損益之金融資產–流動","fill":false,"yAxisID":"公平價值衡量列入其他綜合損益之金融資產–流動","data":[0.026,0.074,0.078,0.12,0.14,0.1,0.13,0.14,0.24,0,0,0,0]},{"label":"短期投資合計","fill":false,"yAxisID":"短期投資合計","data":[0.026,0.074,0.078,0.97,1,0.92,1.01,1.46,2.42,8.74,8.92,7.93,9.97,10.11]}]},
    //   options: {
    //     responsive: true,
    //     hoverMode: 'index',
    //     stacked: false,
    //     title: {
    //       display: true,
    //       text: 'MOMO'
    //     },
    //     scales: {
    //       yAxes: [{
    //         type: 'linear', // only linear but allow scale type registration. This allows extensions to exist solely for log scale for instance
    //         display: true,
    //         position: 'left',
    //         id: '公平價值衡量列入損益之金融資產–流動',
    //       }, {
    //         type: 'linear', // only linear but allow scale type registration. This allows extensions to exist solely for log scale for instance
    //         display: true,
    //         position: 'right',
    //         id: '短期投資合計',

    //         // grid line settings
    //         gridLines: {
    //           drawOnChartArea: false, // only want the grid lines for one axis to show up
    //         },
    //       }],
    //     }
    //   }
    // });

    // window.onload = function() {
    //   var ctx = document.getElementById('canvas').getContext('2d');
    // };
  }
}
</script>