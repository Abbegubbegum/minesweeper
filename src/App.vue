<script setup lang="ts">
import MineGrid from "@/components/MineGrid.vue";
</script>

<template>
  <main>
    <MineGrid height="80vh" />
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
  grid-template: 10vh 80vh 10vh / 10vw 80vw 10vw;
}

main {
  grid-row-start: 2;
  grid-column-start: 2;
}
</style>
