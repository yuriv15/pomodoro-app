<template>
    <div class="gauge-cnt">
        <q-circular-progress
            show-value
            class="text-light-blue q-ma-md"
            :value="initialTimer"
            :min="0"
            :max="maxValue"
            reverse
            :thickness="0.05"
            rounded
            size="300px"
            color="light-blue"
            track-color="light-blue-1"
            center-color="grey-2"
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
import { computed, ref } from 'vue';

const props = defineProps<{
    initialTimer: number;
}>();

const initialTimer = ref<number>(props.initialTimer);
const maxValue = initialTimer.value;
const isTimerPaused = ref<boolean>(true);

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
        }, 1000);
    }
}

function pauseTimer(): void {
    isTimerPaused.value = true;
}

function resetTimer(): void {
    initialTimer.value = maxValue;
}
</script>

<style lang="scss" scoped>
.gauge-cnt {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
</style>
