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
            <span class="text-subtitle2 q-my-md">{{
                $t('pomodoroPage.settings.workTimer')
            }}</span>
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
            <span class="text-subtitle2 q-my-md">{{
                $t('pomodoroPage.settings.restTimer')
            }}</span>
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
            <span class="text-subtitle2 q-my-md">{{
                $t('pomodoroPage.settings.longRestTimer')
            }}</span>
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
            color="primary"
            style="width: 80px"
            :label="$t('global.save')"
        />
    </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { settingsType } from 'pages/Pomodoro/interfaces/settings';

const emit = defineEmits<{
    (e: 'saveSettings'): void;
    (e: 'leaveSettings'): void;
}>();

const configMode = ref<settingsType>('defaultConfig');
const workTimerValue = ref<number>(25);
const restTimerValue = ref<number>(5);
const longRestTimerValue = ref<number>(15);

function handleSaveSettings() {
    emit('saveSettings');
}
</script>

<style lang="scss" scoped></style>
