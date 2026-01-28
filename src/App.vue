<template>
  <header class="bar">
    <div class="bar__container bar__container_big">
      <Grid mini :height="barCardHeight" :per-page="6" :items="selectedLeftItems"></Grid>
    </div>
    <div class="bar__container bar__container">
      <Grid mini :height="barCardHeight" :per-page="1" :items="selectedRightItems"></Grid>
    </div>
  </header>

  <main>
    <div class="wrapper">
      <div class="wrapper__content">
        <Grid v-model="selectedLeftItemsIds" :items="leftBlockItems" :max="6" />
      </div>
      <div class="wrapper__content">
        <Grid v-model="selectedRightItemsIds" :items="rightBlockItems" :max="1" />
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref, computed } from "vue";
import Grid from "./components/Grid.vue";
import { leftBlockItems, rightBlockItems } from "./data/items";

// Сделал высоту бара кастомизируемой
const BAR_HEIGHT = 140;
const barHeight = computed(() => BAR_HEIGHT + "px");
const barCardHeight = computed(() => (BAR_HEIGHT / 3) * 2);
const selectedLeftItemsIds = ref([]);
const selectedRightItemsIds = ref([]);

const selectedLeftItems = computed(() =>
  leftBlockItems.filter((item) => selectedLeftItemsIds.value.includes(item.id)),
);
const selectedRightItems = computed(() =>
  rightBlockItems.filter((item) => selectedRightItemsIds.value.includes(item.id)),
);
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
.bar {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  &__container {
    width: 10%;
    &_big {
      width: 60%;
    }
  }
}
</style>
