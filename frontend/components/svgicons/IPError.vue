<template>
    <!-- Size MUST be an explicit class/unit: with only a viewBox, an svg's
        default width is 100% and Firefox will blow it up to the container
        (Chrome/Safari just happened to mask it with a smaller fallback). -->
    <img src="/ip7-icons/ip7-icon-192.png" class="size-[100px] shrink-0 md:size-[150px]" alt="IP7" />
</template>

<script setup>
import { computed } from 'vue';
import { useMainStore } from '@/store';

const store = useMainStore();
const isDarkMode = computed(() => store.isDarkMode);
</script>
