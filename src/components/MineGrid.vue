<template>
  <div
    class="minefield"
    v-bind:style="'--num-cols:' + width + '; --num-rows:' + height"
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
      height: 0,
      width: 0,

      bombPositions: [] as any[],
    };
  },
  methods: {
    setupGrid(height: number, width: number) {
      this.height = height;
      this.width = width;

      for (let y = 0; y < height; y++) {
        for (let x = 0; x < width; x++) {
          this.patches[this.getIndex(x, y)] = {
            isBomb: false,
          };
        }
      }

      for (let i = 0; i < this.bombCount; i++) {
        let position = {
          x: Math.floor(Math.random() * width),
          y: Math.floor(Math.random() * height),
        };

        while (this.bombPositions.find((pos) => pos === position)) {
          position = {
            x: Math.floor(Math.random() * width),
            y: Math.floor(Math.random() * height),
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
      return y * this.width + x;
    },
  },
  mounted() {
    this.setupGrid(20, 20);
  },
});
</script>

<style scoped>
.minefield {
  display: grid;
  width: fit-content;
  height: fit-content;
  --num-cols: 0;
  --num-rows: 0;
  grid-template-columns: repeat(var(--num-cols), 1fr);
  grid-template-rows: repeat(var(--num-rows), 1fr);
  gap: 0;
}

button {
  height: 1.5rem;
  width: 1.5rem;
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
