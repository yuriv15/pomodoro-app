<template>
    <q-page class="pomodoroItem-cnt">
        <transition-group enter-active-class="animated flipInY">
            <PomodoroGauge
                v-if="!isSettingsMode"
                :initial-timer="timerValue"
                :current-flow="currentFlow"
                :timer-type="timerType"
                :work-timer="workTimerValue"
                :rest-timer="restTimerValue"
                :long-rest-timer="longRestTimerValue"
                @timerCompleted="handleFinishedTimer"
                @openSettings="openSettings"
            />
            <PomodoroSettings
                v-else
                :work-timer="workTimerValue"
                :rest-timer="restTimerValue"
                :long-rest-timer="longRestTimerValue"
                @saveSettings="saveSettings($event)"
                @leaveSettings="leaveSettings"
            />
        </transition-group>
    </q-page>
</template>

<script setup lang="ts">
import PomodoroGauge from './components/PomodoroGauge.vue';
import { computed, ref } from 'vue';
import { TimerType } from 'pages/Pomodoro/interfaces/timer';
import PomodoroSettings from './components/PomodoroSettings.vue';
import { ISettingsValues } from 'pages/Pomodoro/interfaces/settings';

const {
    isSettingsMode,
    workTimerValue,
    restTimerValue,
    longRestTimerValue,
    openSettings,
    saveSettings,
    leaveSettings,
} = usePomodoroSettings();

const { timerType, timerValue, handleFinishedTimer, currentFlow } =
    useGaugeOperation();

function usePomodoroSettings() {
    const isSettingsMode = ref<boolean>(false);
    const workTimerValue = ref<number>(25);
    const restTimerValue = ref<number>(5);
    const longRestTimerValue = ref<number>(15);

    function openSettings(): void {
        isSettingsMode.value = true;
    }

    function leaveSettings(): void {
        isSettingsMode.value = false;
    }

    function saveSettings(pomodoroSettingsValues: ISettingsValues): void {
        workTimerValue.value = pomodoroSettingsValues.workTimerValue;
        restTimerValue.value = pomodoroSettingsValues.restTimerValue;
        longRestTimerValue.value = pomodoroSettingsValues.longRestTimerValue;
        leaveSettings();
    }

    return {
        isSettingsMode,
        workTimerValue,
        restTimerValue,
        longRestTimerValue,
        openSettings,
        saveSettings,
        leaveSettings,
    };
}

function useGaugeOperation() {
    const completedWorkCycles = ref<number>(0);
    const timerType = ref<TimerType>('workTimer');

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

    function handleFinishedTimer(): void {
        if (
            timerType.value === 'longRestTimer' ||
            timerType.value === 'restTimer'
        )
            timerType.value = 'workTimer';
        else {
            completedWorkCycles.value++;
            completedWorkCycles.value % 4 === 0
                ? (timerType.value = 'longRestTimer')
                : (timerType.value = 'restTimer');
        }
    }

    return {
        timerType,
        timerValue,
        currentFlow,
        handleFinishedTimer,
    };
}
</script>

<style lang="scss" scoped>
.pomodoroItem-cnt {
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>
