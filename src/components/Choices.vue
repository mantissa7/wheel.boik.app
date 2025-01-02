<script setup lang="ts">
  import Icon from '@/components/Icon.vue';

  const props = defineProps<{
    labels: string[];
  }>();

  const addRow = (e: Event) => {
    props.labels.push('Vote Gune');
  }

  const eatSliceDontDie = (idx: number) => {
    if (props.labels.length <= 3) {
      return;
    }
    props.labels.splice(idx, 1);
  }
</script>

<template>
  <h2>Choices</h2>
  <form @submit="(e) => e.preventDefault()">
    <div
      class="form-group"
      v-for="(label, idx) in labels"
    >
      <input
        type="text"
        name="label[]"
        v-model="labels[idx]"
      >
      <button
        class="delete-btn"
        :class="{ disabled: props.labels.length <= 3 }"
        :disabled="props.labels.length <= 3"
        type="button"
        @click="eatSliceDontDie(idx)"
      >
        <Icon name="trash-can" />
      </button>
    </div>
    <button
      @click="addRow"
      type="button"
    >
      Add Slice
      <Icon name="plus" />
    </button>
  </form>
</template>

<style>
  form {
    display: grid;
    grid-auto-flow: row;
    gap: 16px;

    .form-group {
      display: grid;
      gap: 16px;
      grid-template-areas: ". input action";
      grid-template-columns: max-content 1fr max-content;

      input {
        grid-area: input;
      }

      button {
        --icon-colour: rgb(215 36 36);
        --border: 1px solid rgb(215 36 36);
        /* --background: rgb(129 34 34 / 68%); */

        grid-area: action;
        aspect-ratio: 1/1;
        display: grid;
        place-items: center;

        &.disabled {
          opacity: 0.1;
          cursor: not-allowed;
        }
      }
    }
  }
</style>
