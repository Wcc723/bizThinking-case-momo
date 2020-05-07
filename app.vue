<template>
  <div>
    <button class="btn btn-outline-primary mr-2" v-for="btn in sourceUrl" :key="btn.name" @click="getData(btn)"
    :class="{ active: selectedSource === btn.name}">
      {{ btn.name }}
    </button>
    <table class="table mt-3 table-hover table-sm table-striped">
      <thead>
        <tr>
          <th v-for="th in heading" :key="th">
            {{ th }}
          </th>
        </tr>
      </thead>
      <tbody is="tr-component" v-for="(list, i) in sourceData" :key="i" :list="list" :prev-data="prevData" :listkey="i">
      </tbody>
    </table>
  </div>
</template>


<script>
import axios from 'axios';
import convert from './convert';
import trComponent from './trComponent';

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
      }],
      selectedSource: {},
    }
  },
  components: {
    trComponent
  },
  methods: {
    sortData() {
      this.prevData = []

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
      axios.get(selectedData.url)
      .then(res => {
        console.log(convert.csvJSON(res.data));
        this.sourceData = JSON.parse(convert.csvJSON(res.data));

        this.heading = []
        for (const key in this.sourceData[0]) {
          this.heading.push(key);
        }
        console.log(this.sourceData, this.heading);
        this.sortData();
      })
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