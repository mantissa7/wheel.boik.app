<script setup lang="ts">
  import { computed, ref, useTemplateRef } from 'vue';
  import Icon from '@/components/Icon.vue';

  const props = defineProps<{
    labels: string[];
  }>();

  const roll = (min: number, max: number) => {
    return Math.floor(Math.random() * (max - min + 1) + min);
  };

  const colourOpts = new Set(['#d11141', '#00b159', '#00aedb', '#f37735', '#ffc425', '#feda75', '#fa7e1e', '#d62976', '#962fbf', '#4f5bd5']);
  const startDeg = roll(1, 360);
  const chunk_r = computed(() => 360 / props.labels.length);

  const wheel = useTemplateRef('wheel');
  const ticker = useTemplateRef('ticker');
  const itvl = ref(0);
  const spinResult = ref('');
  const spinning = ref(false);

  const spinWheel = () => {
    const el = wheel.value;
    clearInterval(itvl.value);
    for (const an of el?.getAnimations() ?? []) {
      an?.cancel();
    }

    const an = el?.animate([
      { rotate: `${roll(1800, 7200)}deg` }
    ], {
      duration: 5000,
      iterations: 1,
      fill: 'forwards',
      composite: 'replace',
      easing: 'ease-out'
      // easing: 'cubic-bezier(1, 0)'
    });

    an?.play();
    spinning.value = true;

    an?.finished.then(() => {
      spinning.value = false;
    })


    const check = () => {
      spinResult.value = '';
      requestAnimationFrame(() => {
        const bb = ticker.value?.getBoundingClientRect();
        const under = document.elementsFromPoint(bb?.x, bb?.y + (bb?.height / 2));
        for (const el of under) {
          if (el.classList.contains('chunk')) {
            spinResult.value = el.textContent ?? '';
          }
        }
        check();
      });
    }
    check();
  }

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
      :style="{ '--startDeg': startDeg, '--alpha': `${chunk_r / 2}deg` }"
      @click="spinWheel"
    >
      <div
        class="chunk"
        v-for="(label, index) in labels"
        :style="{ '--index': index, '--colour': colours[index] }"
      >
        <span :class="[index, label]">{{ label }}</span>
      </div>
    </div>
    <div
      class="ticker"
      ref="ticker"
    >{{ spinResult }}</div>
  </div>

  <button
    :class="{ spinning: spinning }"
    @click="spinWheel"
  >
    Spin
    <Icon name="arrows-spin" />
  </button>
</template>

<style>
  @property --alpha {
    syntax: "<angle>";
    inherits: true;
    initial-value: 1deg;
  }

  @property --beta {
    syntax: "<angle>";
    inherits: true;
    initial-value: 1deg;
  }

  @property --width {
    syntax: "<percentage>";
    inherits: true;
    initial-value: 1%;
  }

  @property --inset {
    syntax: "<percentage>";
    inherits: false;
    initial-value: 1%;
  }

  .wheel-container {
    position: relative
  }

  .wheel {
    width: 500px;
    aspect-ratio: 1/1;
    border-radius: 500px;
    background: rgb(105, 32, 32);
    position: relative;
    overflow: hidden;
    transform: rotate(calc(var(--startDeg) * 1deg));
    --beta: calc(90deg - var(--alpha));

    .chunk {
      --width: calc(sin(var(--alpha)) * 100 / sin(calc(var(--beta))) * 1%);
      --inset: calc((100% - var(--width)) / 2);

      position: absolute;
      inset: 0;
      background: var(--colour);
      display: grid;
      align-items: stretch;
      justify-content: stretch;
      transform: rotate(calc(var(--index) * v-bind(chunk_r)*1deg));
      overflow: hidden;
      clip-path: polygon(var(--inset) 0%, calc(var(--inset) + var(--width)) 0%, 50% 50%);

      span {
        display: grid;
        justify-items: end;
        align-items: center;
        position: absolute;
        inset: 0;
        transform: rotate(-90deg);
        padding-right: 10px;
      }
    }
  }

  .ticker {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 10px;
    width: 100px;
    height: 50px;
    margin: auto;
    transform: translateX(50%);
    filter: drop-shadow(#000 1px 1px 0px)
            drop-shadow(#000 -1px -1px 0px)
            drop-shadow(#000 1px -1px 0px)
            drop-shadow(#000 -1px 1px 0px);

    &::before {
      position: absolute;
      inset: 0;
      content: "";
      /* top: 0;
      bottom: 0;
      right: 10px;
      width: 100px;
      height: 50px; */
      background: #fff;
      margin: auto;
      clip-path: polygon(50% 50%, 100% 20%, 100% 80%);
      /* filter: drop-shadow(2px 3px 5px #000); */
    }
  }

  button.spinning {
    opacity: 0.2;
    pointer-events: none;
  }

</style>
