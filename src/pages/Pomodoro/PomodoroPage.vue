<template>
    <q-page class="pomodoroPage-cnt">
        <transition-group enter-active-class="animated flipInY">
            <PomodoroGauge
                v-if="!isSettingsMode"
                :initial-timer="timerValue"
                :current-flow="currentFlow"
                :timer-type="timerType"
                :work-timer="workTimerValue"
                :rest-timer="restTimerValue"
                :long-rest-timer="longRestTimerValue"
                @timerCompleted="finishedTimer"
                @openSettings="isSettingsMode = true"
            />
            <PomodoroSettings
                v-else
                @saveSettings="saveSettings"
                @leaveSettings="isSettingsMode = false"
            />
        </transition-group>
    </q-page>
</template>

<script lang="ts" setup>
import PomodoroGauge from 'pages/Pomodoro/components/PomodoroGauge.vue';
import { computed, ref } from 'vue';
import { TimerType } from 'pages/Pomodoro/interfaces/timer';
import PomodoroSettings from 'pages/Pomodoro/components/PomodoroSettings.vue';

const isSettingsMode = ref<boolean>(false);
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

const currentFlow = computed(() => {
    switch (timerType.value) {
        case 'workTimer':
            return completedWorkCycles.value + 1;
        case 'restTimer':
            return completedWorkCycles.value;
        case 'longRestTimer':
            return completedWorkCycles.value / 4;
        default:
            const neverOccurs: never = timerType.value;
            return neverOccurs;
    }
});

function saveSettings(): void {
    isSettingsMode.value = false;
}

function finishedTimer(): void {
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

<style lang="scss" scoped>
.pomodoroPage-cnt {
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>
