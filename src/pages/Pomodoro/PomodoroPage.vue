<template>
    <q-page class="flex-center">
        <PomodoroGauge
            :timer-value="timerValue"
            :timer-type="timerType"
            @timerCompleted="finishedTimer"
        />
    </q-page>
</template>

<script lang="ts" setup>
import PomodoroGauge from 'pages/Pomodoro/components/PomodoroGauge.vue';
import { ref } from 'vue';
import { TimerType } from 'pages/Pomodoro/interfaces/timer';

const timerValue = ref<number>(0.05);
const completedCycles = ref<number>(0);

const timerType = ref<TimerType>('workTimer');

function finishedTimer() {
    if (timerType.value === 'longRestTimer' || timerType.value === 'restTimer')
        timerType.value = 'workTimer';
    else {
        completedCycles.value++;
        completedCycles.value % 4 === 0
            ? (timerType.value = 'longRestTimer')
            : (timerType.value = 'restTimer');
    }
}
</script>

<style lang="scss" scoped></style>
