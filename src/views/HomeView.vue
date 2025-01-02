<script setup lang="ts">
  import Choices from '@/components/Choices.vue';
  import Wheel from '@/components/Wheel.vue';
  import { reactive, watch } from 'vue';

  const labels = reactive(['Worms', 'Jinglemon', 'Mount Your Friends', 'Rocket League', 'Ranch Sim', 'Vote Gune', 'Shoot Many Robots', 'Bankrupt', 'Spin Again']);

  watch(labels, (n, o) => {
    console.log(n, o)
  })
</script>

<template>
  <header>
    <h1>Wheel Boik</h1>
  </header>
  <main>
    <div class="wheel-box">
      <Wheel :labels />
    </div>
    <div class="soft-panel">
      <Choices :labels />
    </div>
  </main>
</template>

<style scoped>
  header {
    margin-top: 40px;
  }

  main {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
    grid-auto-flow: column;
    gap: 60px;
    width: 100%;

    >.wheel-box {
      display: grid;
      place-content: center;
      gap: 40px;
    }

    .soft-panel {}
  }
</style>
