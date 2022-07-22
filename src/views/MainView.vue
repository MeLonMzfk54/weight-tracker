<template>
  <div>
    Главная
  </div>
</template>

<script>
import {ref, shallowRef, computed, watch, nextTick} from 'vue'
import Chart from 'chart.js/auto'

export default {
  name: 'MainView',
  setup() {
    const weights = ref([]);
    const weightChartEl = ref(null);
    const weightInput = ref(60.0)

    const weightChart = shallowRef(null)

    const currentWeight = computed(() => {
      return weights.value.sort((a,b) => b.date - a.date)[0] || {weight: 0}
    })

    const addWeight = () => {
      weights.value.push({
        weight: weightInput.value,
        date: new Date().getTime()
      })
    }



  }
}
</script>

<style scoped>

</style>