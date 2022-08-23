<template>
  <div
    class="minefield"
    v-bind:style="
      '--num-cols:' + columns + '; --num-rows:' + rows + '; --height:' + height
    "
  >
    <button
      v-for="(patch, index) in patches"
      type="button"
      :class="{ bomb: patch.isBomb }"
    ></button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      patches: [] as any[],
      bombCount: 10,
      rows: 0,
      columns: 0,

      bombPositions: [] as any[],
    };
  },
  methods: {
    setupGrid(rows: number, columns: number) {
      this.rows = rows;
      this.columns = columns;

      for (let y = 0; y < rows; y++) {
        for (let x = 0; x < columns; x++) {
          this.patches[this.getIndex(x, y)] = {
            isBomb: false,
          };
        }
      }

      for (let i = 0; i < this.bombCount; i++) {
        let position = {
          x: Math.floor(Math.random() * columns),
          y: Math.floor(Math.random() * rows),
        };

        while (this.bombPositions.find((pos) => pos === position)) {
          position = {
            x: Math.floor(Math.random() * columns),
            y: Math.floor(Math.random() * rows),
          };
        }
        this.bombPositions.push(position);

        this.patches[this.getIndex(position.x, position.y)].isBomb = true;
      }
    },
    getPatch(x: number, y: number) {
      return this.patches[this.getIndex(x, y)];
    },
    getIndex(x: number, y: number) {
      return y * this.columns + x;
    },
  },
  props: ["height"],
  mounted() {
    this.setupGrid(20, 20);
  },
});
</script>

<style scoped>
.minefield {
  --height: 0;
  --num-cols: 0;
  --num-rows: 0;
  display: grid;
  width: calc((var(--num-cols) / var(--num-rows)) * var(--height));
  height: var(--height);
  grid-template-columns: repeat(var(--num-cols), 1fr);
  grid-template-rows: repeat(var(--num-rows), 1fr);
  gap: 0;
}

button {
  border-width: 1px;
  background-color: gainsboro;
}

button:hover {
  background-color: grey;
}

.bomb {
  background-color: red;
}
</style>
