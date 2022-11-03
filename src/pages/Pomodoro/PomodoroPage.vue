<template>
    <q-page class="flex-center">
        <PomodoroGauge
            :initial-timer="timerValue"
            :timer-type="timerType"
            :work-timer="workTimerValue"
            :rest-timer="restTimerValue"
            :long-rest-timer="longRestTimerValue"
            @timerCompleted="finishedTimer"
        />
    </q-page>
</template>

<script lang="ts" setup>
import PomodoroGauge from 'pages/Pomodoro/components/PomodoroGauge.vue';
import { computed, ref } from 'vue';
import { TimerType } from 'pages/Pomodoro/interfaces/timer';

const workTimerValue = ref<number>(25);
const restTimerValue = ref<number>(5);
const longRestTimerValue = ref<number>(15);
const timerValue = computed(() => {
    switch (timerType.value) {
        case 'restTimer':
            return restTimerValue.value;
        case 'longRestTimer':
            return longRestTimerValue.value;
        default:
            return workTimerValue.value;
    }
});

const completedWorkCycles = ref<number>(0);

const timerType = ref<TimerType>('workTimer');

function finishedTimer() {
    if (timerType.value === 'longRestTimer' || timerType.value === 'restTimer')
        timerType.value = 'workTimer';
    else {
        completedWorkCycles.value++;
        completedWorkCycles.value % 4 === 0
            ? (timerType.value = 'longRestTimer')
            : (timerType.value = 'restTimer');
    }
}
</script>

<style lang="scss" scoped></style>
