<script setup lang="ts">
import MineGrid from "@/components/MineGrid.vue";
</script>

<template>
  <main>
    <MineGrid />
  </main>
</template>

<style>
* {
  padding: 0;
  margin: 0;
}

#app {
  min-height: 100vh;
  width: 100vw;
  display: grid;
  grid-template: 10vh 1fr 10vh / 10vh 1fr 10vh;
}

main {
  grid-row-start: 2;
  grid-column-start: 2;
}
</style>
