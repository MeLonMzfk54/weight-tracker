<template>
  <main class="weight">

    <h1>Трекер веса</h1>

    <div class="weight__current">
      <span>{{ currentWeight.weight }}</span>
      <small>Текущий вес (кг)</small>
    </div>

    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput">
      <input type="submit" value="Сохранить вес">
    </form>

    <div v-if="weights && weights.length">
      <h2>Последние 7 дней</h2>

      <div class="weight__canvas">
        <canvas ref="weightChartEl"></canvas>
      </div>

      <div class="weight__history">
        <h2>История изменения веса</h2>
        <ul>
          <li v-for="weight in weights" :key="weight.date">
            <span>{{ weight.weight }}кг</span>
            <small>{{ new Date(weight.date).toLocaleDateString() }}</small>
          </li>
        </ul>
      </div>
    </div>
  </main>
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

    return {
      weights,
      weightChartEl,
      weightInput,
      weightChart,
      currentWeight,
      addWeight,
    }
  }
}
</script>

<style scoped>

</style>