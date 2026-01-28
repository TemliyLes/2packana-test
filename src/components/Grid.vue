<template>
  <div class="grid" ref="grid_ref" :style="[gapStyle, wrapHeightStyle]">
    <div
      v-for="(item, index) in items"
      :key="item.id"
      class="grid__elem"
      :style="[elemStyles, positionStyles(index)]"
    >
      <div class="grid__elem__title">{{ item.name }}</div>
      <div class="grid__elem__divider"></div>
      <div class="grid__elem__id">#{{ item.id }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

// Мне было скучно делать эту дефолтную сетку на flex или grid, поэтому я сделал такую тему, тут всё absolute
// Такой подход позволит в дальнейшем делать любые анимации позиционирования этих элементов, например перетаскивания

// Через пропсы можно задать кол-во в строке и высоту
const props = defineProps({
  items: {
    type: Array,
    default: () => [],
  },

  gap: {
    type: Number,
    default: 20,
  },
  height: {
    type: Number,
    default: 200,
  },
  perPage: {
    type: Number,
    default: 3,
  },
});

const grid_ref = ref(null);
const wrapWidth = ref(0);

const elemWidth = computed(() => {
  const totalGap = props.gap * (props.perPage - 1);
  return (wrapWidth.value - totalGap) / props.perPage;
});

const elemStyles = computed(() => {
  const widthStyle = `${elemWidth.value}px`;
  const heightStyle = ` ${props.height}px`;

  return {
    width: widthStyle,
    height: heightStyle,
  };
});

const positionStyles = (index) => {
  const row = Math.floor(index / props.perPage);
  const col = index % props.perPage;

  const top = row * (props.height + props.gap);
  const left = col * (elemWidth.value + props.gap);

  return {
    top: `${top}px`,
    left: `${left}px`,
  };
};

const gapStyle = computed(() => `gap: ${props.gap}px`);

const calcContainer = () => {
  wrapWidth.value = grid_ref.value.offsetWidth;
};

const wrapHeightStyle = computed(() => {
  if (!props.items.length) return {};

  const rows = Math.ceil(props.items.length / props.perPage);
  const height = rows * props.height + (rows - 1) * props.gap;

  return {
    height: `${height}px`,
  };
});

onMounted(() => {
  calcContainer();
  addEventListener("resize", () => {
    requestAnimationFrame(() => {
      calcContainer();
    });
  });
});
</script>

<style lang="scss">
.grid {
  position: relative;
  display: flex;
  flex-wrap: wrap;
  &__elem {
    border: solid 4px #fff;
    background: gray;
    box-shadow: 0 0 4px #00000083;
    flex-shrink: 0;
    position: absolute;
    padding: 20px;
    &__title {
      font-weight: bold;
      color: #040211;
    }
    &__divider {
      background: #04021160;
      width: 100%;
      height: 1px;
      margin: 20px auto;
    }
  }
}
</style>
