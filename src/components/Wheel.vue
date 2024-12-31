<script setup lang="ts">
  import { computed, ref, useTemplateRef } from 'vue';

  const props = defineProps<{
    labels: string[];
  }>();

  const roll = (min: number, max: number) => {
    return Math.floor(Math.random() * (max - min + 1) + min);
  };

  const wheel = useTemplateRef('wheel');
  const ticker = useTemplateRef('ticker');
  const itvl = ref(0);
  const spinResult = ref('');

  const spinWheel = () => {
    const el = wheel.value;
    clearInterval(itvl.value);
    for (const an of el?.getAnimations() ?? []) {
      an?.cancel();
    }

    const an = el?.animate([
      { rotate: '1turn' }
    ], {
      duration: 1000,
      iterations: Number.POSITIVE_INFINITY,
    });

    an?.play();

    itvl.value = setInterval(() => {
      if (an?.playbackRate && (an?.playbackRate < 0.1)) {
        an?.pause();
        return;
      }
      an?.updatePlaybackRate(an.playbackRate * 0.2);
      // an?.updatePlaybackRate(an.playbackRate * 0.8);
    }, 1000);

    const check = () => {
      spinResult.value = '';
      requestAnimationFrame(() => {
        const bb = ticker.value?.getBoundingClientRect();
        const under = document.elementsFromPoint(bb?.x, bb?.y + (bb?.height / 2));
        for (const el of under) {
          if (el.classList.contains('chunk')) {
            spinResult.value = el.textContent;
          }
        }
        if (an?.playState === "paused") {
          console.log(under);
          return;
        }
        // set label
        check();
      });
    }
    check();
  }

  const startDeg = roll(1, 360);
  const chunk_r = 360 / props.labels.length;
  const colourOpts = new Set(['#d11141', '#00b159', '#00aedb', '#f37735', '#ffc425', '#feda75', '#fa7e1e', '#d62976', '#962fbf', '#4f5bd5']);

  const colours = computed(() => {
    const colours: string[] = [];
    for (let i = 0; i < props.labels.length; i++) {
      const choices = colourOpts.difference(new Set(colours));

      if (choices.size === 0) {
        colours.push([...colourOpts.values()][roll(0, colourOpts.size - 1)]);
      }
      colours.push([...choices.values()][roll(0, choices.size - 1)]);
    }
    return colours;
  });
</script>

<template>
  <div class="wheel-container">
    <div
      class="wheel"
      ref="wheel"
      :style="{ '--startDeg': startDeg }"
      @click="spinWheel"
    >
      <div
        class="chunk"
        v-for="(label, index) in labels"
        :style="{ '--index': index, '--colour': colours[index] }"
      >
        <span>{{ label }}</span>
      </div>
    </div>
    <div
      class="ticker"
      ref="ticker"
    >{{ spinResult }}</div>
  </div>
</template>

<style scoped>
  .wheel-container {
    position: relative
  }

  .wheel {
    width: 500px;
    aspect-ratio: 1/1;
    border-radius: 500px;
    background: rgb(105, 32, 32);
    position: relative;
    /* transform: rotate(calc(var(--startdeg) * 1deg)); */

    .chunk {
      position: absolute;
      inset: 0;
      background: conic-gradient(from 55deg, var(--colour) calc(v-bind(chunk_r)*1deg), var(--colour) calc(v-bind(chunk_r)*1deg), transparent calc(v-bind(chunk_r)*1deg));
      border-radius: 100%;
      display: grid;
      align-items: stretch;
      justify-content: stretch;
      padding-right: 10px;
      transform: rotate(calc(var(--index) * v-bind(chunk_r)*1deg));
      overflow: hidden;
      clip-path: polygon(50% 50%, 100% 10%, 100% 90%);

      span {
        display: grid;
        justify-items: end;
        align-items: center;
      }
    }
  }

  .ticker {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    width: 100px;
    height: 50px;
    background: #f006;
    margin: auto;
    transform: translateX(50%);
  }



</style>
