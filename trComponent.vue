<template>
  <tbody>
    <tr>
      <td v-for="(item, key) in list" :key="key" class="text-nowrap">
        {{ item }}
      </td>
    </tr>
    <tr>
      <td v-for="(item, key) in growthRate" :key="key">
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
export default {
  props: ['list', 'prevData', 'listkey'],
  data() {
    return {
      growthRate: {}
    }
  },
  methods: {
    
  },
  created() {
    this.growthRate = {};
    for (const key in this.list) {
      const item = this.list[key];
      // console.log(item, this.prevData[this.listkey][key])
      const growthRate = (item / this.prevData[this.listkey][key] * 100 - 100).toFixed(2)
      this.$set(this.growthRate, key, growthRate)
    }
    // console.log(this.growthRate)
  }
}
</script>