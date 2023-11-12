<script setup lang="ts">
import { ref } from 'vue';

const timer = ref(0);
const timeElapsed = ref(0);
const interval = ref<number | undefined>(undefined);
const state = ref<'stopped' | 'running' | 'paused'>('stopped');

function timerCountTake() {
  if (timeElapsed.value > 0) {
    timeElapsed.value--;
  }
}

function timerCountAdd() {
  if (timeElapsed.value >= 0) {
    timeElapsed.value++;
  }
}

function timerCountReset() {
  timeElapsed.value = 0;
}

function startTimer() {
  if (timeElapsed.value > 0) {
    state.value = 'running';
    timer.value = timeElapsed.value * 60;
    interval.value = setInterval(() => {
      timer.value--;
      if (timer.value === 0) {
        clearInterval(interval.value);
        interval.value = undefined;
      }
    }, 1000);
    timeElapsed.value = 0;
  }
}

function resumeTimer() {
  if (timer.value > 0) {
    state.value = 'running';
    interval.value = setInterval(() => {
      timer.value--;
      if (timer.value === 0) {
        clearInterval(interval.value);
        interval.value = undefined;
      }
    }, 1000);
  }
}

function pauseTimer() {
  state.value = 'paused';
  clearInterval(interval.value);
  interval.value = undefined;
}

function clearTimer() {
  clearInterval(interval.value);
  interval.value = undefined;
  timer.value = 0;
  timeElapsed.value = 0;
}

function formatTime(elapsedTime: any) {
  const minutes = `0${Math.floor(elapsedTime / 60) + ''}`.slice(-2);
  const seconds = `0${elapsedTime % 60 + ''}`.slice(-2);
  return `${minutes}:${seconds}`;
}

</script>

<template>
  <section>
    <div class="timer">
      {{ formatTime(timer) }}
    </div>
    <div class="timer-count">
      <button @click="timerCountTake()">&lt;</button>
      <!-- <input type="number" v-model="timeElapsed" min="0"> -->
      <button @click="timerCountReset()" class="time-elapsed">
        {{ timeElapsed }}
      </button>
      <button @click="timerCountAdd()">&gt;</button>
    </div>
    <div class="buttons">
      <button @click="startTimer()" v-if="state === 'stopped'">Старт</button>
      <button @click="pauseTimer()" v-if="state === 'running'">Стоп</button>
      <button @click="resumeTimer()" v-if="state === 'paused'">Продолжить</button>
      <button @click="clearTimer()" v-if="state === 'running' || state === 'paused' || state === 'stopped'">Сброс</button>
    </div>
  </section>
</template>

<style scoped>
section {
  display: flex;
  flex-direction: column;
  align-items: center;
}

div {
  margin-bottom: 10px;
}

input {
  width: 70px;
}

.timer-count {
  display: flex;
  gap: 10px;
}

.time-elapsed {
  pointer-events: none;
}

.buttons {
  display: flex;
  gap: 10px;
}
</style>