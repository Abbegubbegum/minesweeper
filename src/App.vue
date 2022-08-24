<script setup lang="ts">
import MineGrid from "@/components/MineGrid.vue";
</script>

<template>
  <div class="form-container">
    <div class="input-container">
      <label for="rows">Rows:</label>
      <label for="cols">Columns:</label>
      <label for="bomb-count">Bomb Count:</label>
      <input v-model="rows" type="text" id="rows" />
      <input v-model="cols" type="text" id="cols" />
      <input v-model="bombCount" type="text" id="bomb-count" />
    </div>
    <button
      type="button"
      @click="($refs.minefield as any).setupGrid(rows, cols, bombCount)"
      class="submit-btn"
    >
      Submit
    </button>
  </div>
  <main>
    <MineGrid height="80vh" ref="minefield" />
  </main>
</template>

<script lang="ts">
export default {
  data() {
    return {
      rows: 20,
      cols: 20,
      bombCount: 10,
    };
  },
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
}

#app {
  min-height: 100vh;
  width: 100vw;
  display: grid;
  grid-template: 10vh 80vh 10vh / 10vw 80vw 10vw;
}

main {
  grid-row-start: 2;
  grid-column-start: 2;
}

.form-container {
  display: flex;
  align-items: flex-start;
}

.input-container {
  display: grid;
  grid-template-rows: 1fr 1fr;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 0.3rem;
}

label {
  font-weight: bold;
  font-size: 1.2rem;
  margin-left: 0.2rem;
}

.submit-btn {
  display: inline;
  align-self: center;
  margin: 0.5rem;
  padding: 0.3rem;
}
</style>
