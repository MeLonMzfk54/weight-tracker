<template>
  <main class="weight">

    <h1>Трекер веса</h1>

    <div class="weight__current">
      <span>{{ currentWeight.weight }}</span>
      <small>Текущий вес (кг)</small>
    </div>

    <form class="weight__form" @submit.prevent="addWeight">
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

    watch(weights.value, newWeights => {
      const ws = [...newWeights]

      if (weightChart.value) {
        weightChart.value.data.labels = ws.sort((a, b) => a.date - b.date)
            .map(weight => new Date(weight.date).toLocaleDateString())
            .slice(-7)

        weightChart.value.data.datasets[0].data = ws.sort((a, b) => a.date - b.date)
            .map(weight => weight.weight)
            .slice(-7)

        weightChart.value.update();

        return
      }

      nextTick(() => {
        weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
          type: 'line',
          data: {
            labels: ws.sort((a, b) => a.date - b.date)
                .map(weight => new Date(weight.date).toLocaleDateString()),
            datasets: [
              {
                label: 'Вес',
                data: ws.sort((a, b) => a.date - b.date)
                    .map(weight => weight.weight),
                backgroundColor: 'rgba(255, 105, 180, .2)',
                borderColor: 'rgb(255, 105, 180)',
                borderWidth: 1,
                fill: true,
              }
            ]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
          }
        })
      })
    })

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

<style scoped lang="scss">
body {
  background-color: #eee;
}
main {
  padding: 1.5rem;
}
h1 {
  font-size: 2em;
  text-align: center;
  margin-bottom: 2rem;
}
h2 {
  margin-bottom: 1rem;
  color: #888;
  font-weight: 400;
}
.weight {
  &__current {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 200px;
    height: 200px;
    text-align: center;
    background-color: white;
    border-radius: 999px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    border: 5px solid hwb(330 41% 0%);
    margin: 0 auto 2rem;

    span {
      display: block;
      font-size: 2em;
      font-weight: bold;
      margin-bottom: 0.5rem;
    }

    small {
      color: #888;
      font-style: italic;
    }
  }

  &__form {
    display: flex;
    margin-bottom: 2rem;
    border: 1px solid #AAA;
    border-radius: 0.5rem;
    overflow: hidden;
    transition: 200ms linear;
    &:focus-within,
    &:hover {
      border-color: hotpink;
      border-width: 2px;
    }

    input[type='number'] {
      appearance: none;
      outline: none;
      border: none;
      background-color: white;
      flex: 1 1 0%;
      padding: 1rem 1.5rem;
      font-size: 1.25rem;
    }

    input[type="submit"] {
      appearance: none;
      outline: none;
      border: none;
      cursor: pointer;
      background-color: hotpink;
      padding: 0.5rem 1rem;
      color: white;
      font-size: 1.25rem;
      font-weight: 700;
      transition: 200ms linear;
      border-left: 3px solid transparent;
      &:hover {
        background-color: white;
        color: hotpink;
        border-left-color: hotpink;
      }
    }
  }
  &__canvas {
    width: 100%;
    max-width: 720px;
    background-color: white;
    padding: 1rem;
    border-radius: 0.5rem;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    margin-bottom: 2rem;
  }
  &__history {
    ul {
      list-style: none;
      padding: 0;
      margin: 0;
      li {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0.5rem;
        cursor: pointer;
        border: 1px solid hotpink;
        border-bottom: none;
        &:hover {
          background-color: #f8f8f8;
        }
        &:last-of-type {
          border: 1px solid hotpink;
        }

        span {
          display: block;
          font-size: 1.25rem;
          font-weight: 700;
          margin-right: 1rem;
        }

        small {
          color: #888;
          font-style: italic;
        }
      }
    }
  }
}
</style>