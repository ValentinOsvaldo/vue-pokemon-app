<template>
  <div class="options-container">
    <button
      as="button"
      v-for="pokemon in pokemons"
      :key="pokemon.id"
      :disabled="showOptions ? false : true"
      @click="$emit('selection', pokemon.id)"
    >
      {{ pokemon.name }}
    </button>
  </div>
</template>

<script>
export default {
  props: {
    pokemons: {
      type: Array,
      required: true,
    },
    showOptions: {
      type: Boolean,
      required: true,
      default: true,
    },
  },
  created() {
    // props are exposed on `this`
    console.log('Component showOptions :' + this.showOptions);
  },
  watch: {
    showOptions() {
      console.log('Component showOptions: ' + this.showOptions);
    },
  },
};
</script>

<style scoped>
button {
  background-color: white;
  border-radius: 5px;
  border: 1px solid rgba(0, 0, 0, 0.2);
  cursor: pointer;
  font-size: 1rem;
  margin-bottom: 10px;
  padding: 0.5rem 0.25rem;
  width: 250px;
}

button:hover {
  background-color: rgba(0, 0, 0, 0.05);
}
button:disabled {
  pointer-events: none;
  opacity: 0.6;
}
.options-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-flow: column nowrap;
  margin-top: 1rem;
}
</style>
