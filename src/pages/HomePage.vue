<template>
    <q-page class="flex-center">
        <PomodoroGauge :initial-timer="5" />
    </q-page>
</template>

<script lang="ts" setup>
import PomodoroGauge from 'pages/components/PomodoroGauge.vue';
</script>

<style lang="scss" scoped></style>
