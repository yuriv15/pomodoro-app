<template>
    <main class="q-gutter-y-lg">
        <section class="q-gutter-x-sm">
            <q-btn
                icon="arrow_back"
                @click="$emit('leaveSettings')"
                flat
                round
            />
            <span class="text-subtitle1">{{
                $t('pomodoroPage.settings.title')
            }}</span>
        </section>
        <section>
            <q-btn-toggle
                v-model="configMode"
                toggle-color="primary"
                unelevated
                style="border: 1px solid #027be3"
                :options="[
                    { label: 'Default', value: 'defaultConfig' },
                    { label: 'Long', value: 'longConfig' },
                    { label: 'Custom', value: 'customConfig' },
                ]"
            />
        </section>
        <section>
            <span class="text-subtitle2 q-my-md">
                <span>
                    {{ $t('pomodoroPage.settings.workTimer') }}
                </span>
                -
                <span>
                    {{
                        `${workTimerValue} ${$t(
                            'pomodoroPage.minutesQuantity',
                            workTimerValue
                        )}`
                    }}
                </span>
            </span>
            <q-slider
                v-model="workTimerValue"
                :min="5"
                :max="120"
                :step="5"
                snap
                label
                switch-label-side
            />
        </section>
        <section>
            <span class="text-subtitle2 q-my-md">
                <span>
                    {{ $t('pomodoroPage.settings.restTimer') }}
                </span>
                -
                <span>
                    {{
                        `${restTimerValue} ${$t(
                            'pomodoroPage.minutesQuantity',
                            restTimerValue
                        )}`
                    }}
                </span>
            </span>
            <q-slider
                v-model="restTimerValue"
                :min="5"
                :max="120"
                :step="5"
                snap
                label
                switch-label-side
            />
        </section>
        <section>
            <span class="text-subtitle2 q-my-md">
                <span>
                    {{ $t('pomodoroPage.settings.longRestTimer') }}
                </span>
                -
                <span>
                    {{
                        `${longRestTimerValue} ${$t(
                            'pomodoroPage.minutesQuantity',
                            longRestTimerValue
                        )}`
                    }}
                </span>
            </span>
            <q-slider
                v-model="longRestTimerValue"
                :min="5"
                :max="120"
                :step="5"
                snap
                label
                switch-label-side
            />
        </section>
        <q-btn
            @click="handleSaveSettings"
            :disabled="!hasUnsavedInfo"
            color="primary"
            style="width: 80px"
            :label="$t('global.save')"
        />
    </main>
</template>

<script setup lang="ts">
import { computed, onBeforeMount, ref } from 'vue';
import {
    ISettingsValues,
    settingsType,
} from 'pages/Pomodoro/interfaces/settings';
import { Md5 } from 'ts-md5';

const props = defineProps<{
    workTimer: number;
    restTimer: number;
    longRestTimer: number;
}>();

const emit = defineEmits<{
    (e: 'saveSettings', settings: ISettingsValues): void;
    (e: 'leaveSettings'): void;
}>();

const defaultConfig = {
    workTimerValue: 25,
    restTimerValue: 5,
    longRestTimerValue: 15,
};

const longConfig = {
    workTimerValue: 50,
    restTimerValue: 10,
    longRestTimerValue: 30,
};

const workTimerValue = ref<number>(defaultConfig.workTimerValue);
const restTimerValue = ref<number>(defaultConfig.restTimerValue);
const longRestTimerValue = ref<number>(defaultConfig.longRestTimerValue);

const initialConfigHash = ref<string>();

const configMode = computed<settingsType>({
    get() {
        if (
            workTimerValue.value === defaultConfig.workTimerValue &&
            restTimerValue.value === defaultConfig.restTimerValue &&
            longRestTimerValue.value === defaultConfig.longRestTimerValue
        )
            return 'defaultConfig';
        else if (
            workTimerValue.value === longConfig.workTimerValue &&
            restTimerValue.value === longConfig.restTimerValue &&
            longRestTimerValue.value === longConfig.longRestTimerValue
        )
            return 'longConfig';
        else return 'customConfig';
    },
    set(pomodoroConfig: settingsType) {
        switch (pomodoroConfig) {
            case 'defaultConfig':
                workTimerValue.value = defaultConfig.workTimerValue;
                restTimerValue.value = defaultConfig.restTimerValue;
                longRestTimerValue.value = defaultConfig.longRestTimerValue;
                break;
            case 'longConfig':
                workTimerValue.value = longConfig.workTimerValue;
                restTimerValue.value = longConfig.restTimerValue;
                longRestTimerValue.value = longConfig.longRestTimerValue;
                break;
            case 'customConfig':
                break;
        }
    },
});

const configurationState = computed(() =>
    JSON.stringify({
        workTimerValue: workTimerValue.value,
        restTimerValue: restTimerValue.value,
        longRestTimerValue: longRestTimerValue.value,
    })
);

const configHash = computed(() => Md5.hashStr(configurationState.value));

const hasUnsavedInfo = computed(
    () => initialConfigHash.value !== configHash.value
);

function handleSaveSettings() {
    emit('saveSettings', {
        workTimerValue: workTimerValue.value,
        restTimerValue: restTimerValue.value,
        longRestTimerValue: longRestTimerValue.value,
    });
}

onBeforeMount(() => {
    workTimerValue.value = props.workTimer;
    restTimerValue.value = props.restTimer;
    longRestTimerValue.value = props.longRestTimer;
    initialConfigHash.value = configHash.value;
});
</script>

<style lang="scss" scoped></style>
