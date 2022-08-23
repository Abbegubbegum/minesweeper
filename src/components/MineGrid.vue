<template>
  <div class="grid">
    <div v-for="(patchRow, y) in patches" class="row">
      <button v-for="(patch, x) in patchRow" type="button"></button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      patches: [] as Object[][],
    };
  },
  methods: {
    setupGrid() {
      this.patches = new Array(50);
      for (let i = 0; i < 50; i++) {
        this.patches[i] = new Array(50);
        for (let j = 0; j < 50; j++) {
          this.patches[i][j] = {
            isBomb: false,
          };
        }
      }
    },
  },

  mounted() {
    this.setupGrid();
  },
});
</script>

<style>
.grid {
  display: grid;
  height: auto;
  width: auto;
  grid-auto-flow: row;
}

.row {
  display: grid;
  grid-auto-flow: column;
}

button {
  height: 1vw;
  width: 1vw;
}
</style>
