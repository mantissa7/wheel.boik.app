<script setup lang="ts">
    const props = defineProps<{
        name: string;
    }>();
</script>

<template>
    <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
        <use :href="`/icons/${name}.svg#icon`"></use>
    </svg>
</template>

<style>
    svg {
        width: 100%;
        height: 100%;
    }
</style>