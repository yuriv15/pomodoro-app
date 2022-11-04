<template>
    <main class="gauge-cnt">
        <q-circular-progress
            show-value
            class="q-ma-md"
            :class="`text-${gaugeColor}`"
            :value="gaugeTimer"
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
            <div class="gauge-display-cnt">
                <span> {{ minutes }}:{{ seconds }} </span>
                <small class="text-subtitle2">{{
                    $t(`pomodoroPage.currentFlow.${timerType}`, { currentFlow })
                }}</small>
            </div>
        </q-circular-progress>
        <div class="q-gutter-md">
            <q-btn
                v-show="gaugeTimer !== 0 && isTimerPaused"
                @click="startTimer"
                color="primary"
                style="width: 80px"
                :label="$t('pomodoroPage.start')"
            />
            <q-btn
                v-show="
                    initialTimer * 60 !== gaugeTimer &&
                    !isTimerPaused &&
                    gaugeTimer !== 0
                "
                @click="pauseTimer"
                color="red-8"
                text-color="white"
                style="width: 80px"
                :label="$t('pomodoroPage.stop')"
            />
            <q-btn
                v-show="isTimerPaused && initialTimer * 60 !== gaugeTimer"
                @click="handleResetTimer"
                class="q-mt-md"
                color="grey-4"
                text-color="black"
                style="width: 80px"
                :label="$t('pomodoroPage.resetTimer')"
            />
        </div>
        <q-btn
            class="settings-btn"
            icon="settings"
            round
            unelevated
            @click="$emit('openSettings')"
        />
    </main>
</template>

<script lang="ts" setup>
import { computed, nextTick, ref, watch } from 'vue';
import { TimerType } from 'pages/Pomodoro/interfaces/timer';
import { useQuasar } from 'quasar';
import { useI18n } from 'vue-i18n';

const props = defineProps<{
    initialTimer: number;
    timerType: TimerType;
    workTimer: number;
    restTimer: number;
    longRestTimer: number;
    currentFlow: number;
}>();

const $q = useQuasar();
const $i18n = useI18n();
const gaugeTimer = ref<number>(props.initialTimer * 60);
const isTimerPaused = ref<boolean>(true);
const emit = defineEmits<{
    (e: 'timerCompleted'): void;
    (e: 'openSettings'): void;
}>();

const maxValue = computed(() => {
    return props[props.timerType] * 60;
});

const minutes = computed(() => {
    return Math.floor(gaugeTimer.value / 60).toLocaleString('en-us', {
        minimumIntegerDigits: 2,
    });
});

const seconds = computed(() => {
    return (gaugeTimer.value % 60).toLocaleString('en-us', {
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
            if (gaugeTimer.value === 0 || isTimerPaused.value) {
                isTimerPaused.value = true;
                clearInterval(interval);
            } else {
                gaugeTimer.value--;
            }
        }, 1000);
    }
}

function pauseTimer(): void {
    isTimerPaused.value = true;
}

function resetTimer(): void {
    gaugeTimer.value = maxValue.value;
}

function handleResetTimer() {
    $q.dialog({
        title: $i18n.t('pomodoroPage.resetTimerDialog.title'),
        message: $i18n.t('pomodoroPage.resetTimerDialog.message'),
        cancel: true,
    }).onOk(() => {
        resetTimer();
    });
}

watch(gaugeTimer, async (timer) => {
    if (timer === 0) {
        emit('timerCompleted');
        await nextTick();
        gaugeTimer.value = maxValue.value;
    }
});
</script>

<style lang="scss" scoped>
.gauge-cnt {
    display: flex;
    width: fit-content;
    position: relative;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    .gauge-display-cnt {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .settings-btn {
        position: absolute;
        top: 0;
        right: 0;
    }
}
</style>
