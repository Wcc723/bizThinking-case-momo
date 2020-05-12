<template>
  <div class="container-fluid">
    
    
    <div class="my-3">
      <button class="btn btn-outline-primary mr-2" v-for="btn in sourceUrl" :key="btn.name" @click="getData(btn)"
      :class="{ active: selectedSource === btn.name}">
        {{ btn.name }}
      </button>
    </div>
    <chart-component :cartData="filterData" :heading="heading" :inTimeOrder="inTimeOrder"></chart-component>
    <div>
      <label v-for="(item, key) in dataName " :key="key" class="mr-2">
        <input type="checkbox" :value="item" v-model="selectedItem">
        {{ item }}
      </label>
      <br>
      <label class="mr-2">
        <input type="radio" v-model="inTimeOrder" :value="true" class="mr-1"> 正向
      </label>
      <label class="mr-2">
        <input type="radio" v-model="inTimeOrder" :value="false" class="mr-1"> 逆向
      </label>
      
    </div>
    <table class="table mt-3 table-hover table-sm table-striped">
      <thead>
        <tr>
          <th>名稱</th>
          <th v-for="th in inTimeOrder ?
            heading.filter(item => item !== '名稱'):
            heading.filter(item => item !== '名稱').reverse()" :key="th">
            {{ th }}
          </th>
        </tr>
      </thead>
      <tbody is="tr-component" v-for="(list, i) in sourceData" :key="i" :list="list"
      :inTimeOrder="inTimeOrder" :prev-data="prevData" :listkey="i" v-on:select-item="getSelect">
      </tbody>
    </table>
  </div>
</template>


<script>
import axios from 'axios';
import convert from './convert';
import trComponent from './trComponent';
import chartComponent from './chart';

export default {
  data() {
    return {
      sourceData: [],
      data: {},
      arrayData: [],
      heading: [],
      prevData: [],
      sourceUrl: [{
        name: '資產負債表',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=0&single=true&output=csv',
      },{
        name: '損益表',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=1458651561&single=true&output=csv',
      },{
        name: '現金流量表',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=321885516&single=true&output=csv',
      },{
        name: '第一季同期損益表',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=777097944&single=true&output=csv',
      },{
        name: '第四季同期損益表',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=1914969944&single=true&output=csv',
      },{
        name: '第一季同期現金流量',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=1623141648&single=true&output=csv',
      },{
        name: '第四季同期現金流量',
        url: 'https://docs.google.com/spreadsheets/d/e/2PACX-1vTdJTQF_ZnIbH-VAs5CkumipbKNA3Yen9Sciei7y2Ci813oSD0Nhee4OLFRVJDDAk3s8K8GRLskR5TQ/pub?gid=452574495&single=true&output=csv',
      }],
      dataName: [],
      selectedSource: {},
      selectedItem: [],
      inTimeOrder: true
    }
  },
  components: {
    trComponent,
    chartComponent
  },
  methods: {
    sortData() {
      this.prevData = []

      // 順排的資料，取出前一筆
      this.sourceData.forEach((list, i) => {
        let prev = 0;
        let newList = convert.reverseObject({...list})
        
        this.prevData.push([])
        for (const key in newList) {
          // this.prevData[i][key] = prev;
          this.$set(this.prevData[i], key, prev)
          prev = list[key];
        }
      });
      console.log('prevData', this.prevData);
    },
    getData(selectedData = this.sourceUrl[0]) {
      this.selectedSource = selectedData.name
      this.sourceData = [];
      this.dataName = []
      axios.get(selectedData.url)
      .then(res => {
        // console.log(convert.csvJSON(res.data));
        this.sourceData = JSON.parse(convert.csvJSON(res.data));

        this.heading = []
        for (const key in this.sourceData[0]) {
          this.heading.push(key);
        }
        this.dataName = this.sourceData.map(item => item['名稱'])
        // console.log(this.sourceData, this.heading);
        this.sortData();
      })
    },
    getSelect(name, selected) {
      console.log(name, selected)
    }
  },
  computed: {
    filterData() {
      return this.sourceData.filter(item => this.selectedItem.includes(item['名稱']));
    }
  },
  watch: {
    heading() {
      if (this.inTimeOrder) {
        return this.heading;
      } else {
        this.heading = this.heading.reverse();
      }
    }
  },
  created() {
    this.getData();
    
  }
}
</script>


<style lang="scss">
@import './node_modules/bootstrap/scss/bootstrap';
</style>