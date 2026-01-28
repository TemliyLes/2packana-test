<template>
  <header></header>

  <main>
    <div class="wrapper">
      <div class="wrapper__content">
        <Grid :items="leftBlockItems" />
      </div>
      <div class="wrapper__content">
        <Grid :items="rightBlockItems" />
      </div>
    </div>
  </main>
</template>

<script setup>
import { computed } from "vue";
import Grid from "./components/Grid.vue";
import { leftBlockItems, rightBlockItems } from "./data/items";

// Сделал высоту бара кастомизируемой
const BAR_HEIGHT = 140;
const barHeight = computed(() => BAR_HEIGHT + "px");
</script>

<style scoped lang="scss">
header {
  position: fixed;
  height: v-bind(barHeight);
  width: 100%;
  background: #ddd;
  z-index: 34;
  box-shadow: 0 0 12px #0000007b;
  border-bottom: solid 4px #fff;
}
.wrapper {
  padding-top: v-bind(barHeight);
  display: flex;
  gap: 20px;
  &__content {
    width: 50%;
    padding: 20px;
  }
}
</style>
