<template>
    <img src="/ip7-icons/ip7-icon-192.png" width="28" height="28" alt="IP7" />
</template>

<script setup>
import { computed } from 'vue';
import { useMainStore } from '@/store';

const store = useMainStore();
const isDarkMode = computed(() => store.isDarkMode);
</script>
