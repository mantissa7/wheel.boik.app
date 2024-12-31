<script setup lang="ts">
import Wheel from '@/components/Wheel.vue';

const labels = ['Vote', 'Gune', 'Shoot Many Robots', 'Ranch Sim', 'Bankrupt', 'Spin Again'];
</script>

<template>
  <main>
    <h1>Wheel Boik</h1>
    <Wheel :labels/>
  </main>
</template>
