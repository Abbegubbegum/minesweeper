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
      :class="[
        { bomb: patch.isBomb, revealed: patch.isRevealed },
        labelClass(patch),
      ]"
      @click="patchClick(patch)"
      @contextmenu.prevent="toggleFlag(patch)"
    >
      {{ patchLabel(patch) }}
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      patches: [] as any[],
      bombCount: 30,
      rows: 0,
      columns: 0,

      bombPositions: [] as any[],
      gameover: false,
    };
  },

  methods: {
    setupGrid(rows: number, columns: number, bombCount: number) {
      //set row and column to param
      this.rows = rows;
      this.columns = columns;
      this.bombCount = bombCount;
      this.patches = [];
      this.bombPositions = [];
      this.gameover = false;

      //instantiate the empty grid
      for (let y = 0; y < rows; y++) {
        for (let x = 0; x < columns; x++) {
          this.patches[this.getIndex(x, y)] = {
            x: x,
            y: y,
            isBomb: false,
            isRevealed: false,
            isFlagged: false,
            neighbors: [],
            bombCount: 0,
          };
        }
      }

      // console.log(this.patches);

      //Create bombs
      for (let i = 0; i < this.bombCount; i++) {
        let pos = {
          x: Math.floor(Math.random() * columns),
          y: Math.floor(Math.random() * rows),
        };

        //Make sure the random position doesn't have a bomb
        while (this.getPatch(pos.x, pos.y).isBomb === true) {
          pos = {
            x: Math.floor(Math.random() * columns),
            y: Math.floor(Math.random() * rows),
          };
        }

        //Debugging only probably
        this.bombPositions.push(pos);

        //Add the bomb
        this.getPatch(pos.x, pos.y).isBomb = true;
      }

      //Setup bombcounts and neighbors
      this.patches.forEach((patch) => {
        if (patch.isBomb) return;

        // console.log(patch.neighbors);
        // console.log(this.fetchNeighbors(patch.x, patch.y));

        patch.neighbors = this.fetchNeighbors(patch.x, patch.y);

        let bombCount = 0;
        patch.neighbors.forEach((neighbor: any) => {
          if (neighbor.isBomb) bombCount++;
        });

        patch.bombCount = bombCount;
      });
    },

    getPatch(x: number, y: number) {
      return this.patches[this.getIndex(x, y)];
    },
    getIndex(x: number, y: number) {
      return y * this.columns + x;
    },

    fetchNeighbors(x: number, y: number) {
      let neighbors = [];
      for (let tempY = y - 1; tempY <= y + 1; tempY++) {
        //If tempY is valid, loop through the x
        if (tempY >= 0 && tempY < this.rows) {
          for (let tempX = x - 1; tempX <= x + 1; tempX++) {
            //If it is not the original position, continue
            if (tempX === x && tempY === y) continue;

            //If tempX is valid, add the position
            if (tempX >= 0 && tempX < this.columns) {
              neighbors.push(this.getPatch(tempX, tempY));
            }
          }
        }
      }

      return neighbors;
    },

    toggleFlag(patch: any) {
      if (patch.isRevealed || this.gameover) return;

      patch.isFlagged = !patch.isFlagged;
    },

    patchClick(patch: any) {
      if (patch.isFlagged || this.gameover) return;

      if (patch.isBomb === true) {
        console.log("Fail!");
        this.gameover = true;
        this.revealAllBombs();
      } else {
        this.revealPatch(patch);
      }
    },

    revealPatch(patch: any) {
      if (patch.isFlagged || patch.isRevealed) return;

      patch.isRevealed = true;

      if (patch.bombCount === 0) {
        patch.neighbors.forEach((neighbor: any) => {
          this.revealPatch(neighbor);
        });
      }
    },

    patchLabel(patch: any) {
      if (patch.isFlagged) return "🚩";

      if (patch.isRevealed === false) return "";

      if (patch.isBomb) return "💣";

      if (patch.bombCount === 0) return "";

      return patch.bombCount;
    },

    labelClass(patch: any) {
      if (!patch.isRevealed) return "";

      return "label-" + patch.bombCount;
    },

    revealAllBombs() {
      this.bombPositions.forEach((pos) => {
        if (!this.getPatch(pos.x, pos.y).isFlagged)
          this.getPatch(pos.x, pos.y).isRevealed = true;
      });
    },
  },

  props: ["height"],

  mounted() {
    this.setupGrid(20, 20, 20);
  },
});
</script>

<style scoped>
.minefield {
  --height: 0;
  --width: calc((var(--num-cols) / var(--num-rows)) * var(--height));
  --num-cols: 0;
  --num-rows: 0;
  display: grid;
  width: var(--width);
  height: var(--height);
  grid-template-columns: repeat(var(--num-cols), 1fr);
  grid-template-rows: repeat(var(--num-rows), 1fr);
  gap: 0;
}

button {
  border-width: 1px;
  background-color: gainsboro;
  --font-percentage: 0.7;
  font-size: calc(
    min(
      var(--height) / var(--num-rows) * var(--font-percentage),
      var(--width) / var(--num-cols) * var(--font-percentage)
    )
  );
  font-weight: bold;
}

button:hover {
  background-color: grey;
}

.revealed {
  background-color: white;
}

.label-1 {
  color: blue;
}
.label-2 {
  color: green;
}
.label-3 {
  color: red;
}
.label-4 {
  color: purple;
}
.label-5 {
  color: maroon;
}
.label-6 {
  color: turquoise;
}
.label-7 {
  color: black;
}

.label-8 {
  color: grey;
}
</style>
