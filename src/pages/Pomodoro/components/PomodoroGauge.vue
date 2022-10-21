<template>
    <div class="gauge-cnt">
        <q-circular-progress
            show-value
            class="q-ma-md"
            :class="`text-${gaugeColor}`"
            :value="initialTimer"
            :min="0"
            :max="maxValue"
            reverse
            :thickness="0.05"
            rounded
            size="300px"
            :color="gaugeColor"
            :track-color="`${gaugeColor}-2`"
            center-color="grey-1"
            font-size="70px"
        >
            {{ minutes }}:{{ seconds }}
        </q-circular-progress>
        <div class="q-gutter-md">
            <q-btn
                v-show="initialTimer !== 0"
                @click="startTimer"
                color="primary"
                style="width: 80px"
                :label="$t('pomodoroPage.start')"
            />
            <q-btn
                v-show="
                    initialTimer !== props.initialTimer &&
                    !isTimerPaused &&
                    initialTimer !== 0
                "
                @click="pauseTimer"
                color="red-8"
                text-color="white"
                style="width: 80px"
                :label="$t('pomodoroPage.stop')"
            />
            <q-btn
                v-show="isTimerPaused && initialTimer !== props.initialTimer"
                @click="resetTimer"
                class="q-mt-md"
                color="grey-4"
                text-color="black"
                style="width: 80px"
                :label="$t('pomodoroPage.resetTimer')"
            />
        </div>
    </div>
</template>

<script lang="ts" setup>
import { computed, onBeforeMount, ref, watch } from 'vue';
import { TimerType } from 'pages/Pomodoro/interfaces/timer';
import { cloneDeep } from 'src/utils/variable.functions';

const props = defineProps<{
    timerValue: number;
    timerType: TimerType;
    workTimer: number;
    restTimer: number;
    longRestTimer: number;
}>();

const initialTimer = ref<number>(props.timerValue * 60);
const maxValue = ref<number>(0);
const isTimerPaused = ref<boolean>(true);
const emit = defineEmits<{
    (e: 'timerCompleted'): void;
}>();

const minutes = computed(() => {
    return Math.floor(initialTimer.value / 60).toLocaleString('en-us', {
        minimumIntegerDigits: 2,
    });
});

const seconds = computed(() => {
    return (initialTimer.value % 60).toLocaleString('en-us', {
        minimumIntegerDigits: 2,
    });
});

const gaugeColor = computed(() => {
    return props.timerType === 'workTimer' ? 'light-blue' : 'amber';
});

function startTimer(): void {
    if (isTimerPaused.value) {
        isTimerPaused.value = false;
        const interval = setInterval(() => {
            if (initialTimer.value === 0 || isTimerPaused.value) {
                isTimerPaused.value = true;
                clearInterval(interval);
            } else {
                initialTimer.value--;
            }
        }, 0.001);
    }
}

function pauseTimer(): void {
    isTimerPaused.value = true;
}

function resetTimer(): void {
    initialTimer.value = Number(cloneDeep(maxValue.value));
}

watch(initialTimer, async (timer) => {
    if (timer === 0) {
        emit('timerCompleted');
        await setTimeout(() => {
            maxValue.value = Number(cloneDeep(props[props.timerType]));
            initialTimer.value = Number(cloneDeep([props.timerType])) * 60;
        }, 300);
    }
});

onBeforeMount(() => {
    maxValue.value = Number(cloneDeep([props.timerType]));
});
</script>

<style lang="scss" scoped>
.gauge-cnt {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
</style>
