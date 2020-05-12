<template>
  <tbody>
    <tr>
      <td>{{ list['名稱'] }}</td>
      <template v-for="(item, key) in listData">
        <td :key="key" class="text-nowrap" v-if="key !== '名稱'">
          {{ item }}
        </td>
      </template>
    </tr>
    <tr>
      <td></td>
      <td v-for="(item, key) in growthRateData" :key="key">
        <div v-if="!isNaN(item) && isFinite(item)"
        :class="{
          'text-danger': item > 0,
          'text-success': item < 0,
        }">
        {{ item }}%
        </div>
      </td>
    </tr>
  </tbody>
</template>


<script>
import convert from './convert';

export default {
  props: ['list', 'prevData', 'listkey', 'inTimeOrder'],
  data() {
    return {
      growthRate: {},
      selectedItem: false,
    }
  },
  methods: {
    
  },
  watch: {
    selectedItem() {
      this.$emit('selectItem', this.list[0], this.selectedItem)
    }
  },
  computed: {
    listData() {
      if (this.inTimeOrder) {
        return this.list
      } else {
        return convert.reverseObject(this.list);
      }
    },
    growthRateData() {
      if (this.inTimeOrder) {
        return this.growthRate;
      } else {
        return convert.reverseObject(this.growthRate);
      }
    }
  },
  created() {
    // 順排的資料排序
    this.growthRate = {};
    for (const key in this.list) {
      const item = this.list[key];
      // console.log(item, this.prevData[this.listkey][key])
      const growthRate = (item / this.prevData[this.listkey][key] * 100 - 100).toFixed(2)
      if (key !== '名稱') {
        this.$set(this.growthRate, key, growthRate)
      }
    }
    // console.log(this.growthRate)
  }
}
</script>