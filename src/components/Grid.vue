<template>
  <div class="grid" ref="grid_ref" :style="[gapStyle, wrapHeightStyle]">
    <TransitionGroup name="fade">
      <div
        v-for="(item, index) in items"
        :key="item.id"
        class="grid__elem"
        @click="onClick(item.id)"
        :style="[elemStyles, positionStyles(index), pointerStyle, paddingStyle, durationStyle]"
      >
        <div class="grid__elem__title">{{ item.name }}</div>
        <div class="grid__elem__divider" :style="lineMarginStyle"></div>
        <div class="grid__elem__id">#{{ item.id }}</div>
        <div
          v-if="modelValue"
          class="grid__elem__check"
          :style="durationStyle"
          :class="{ grid__elem__check_checked: testCheck(item.id) }"
        >
          <div class="grid__elem__check__icon">
            <Check />
          </div>
        </div>
      </div>
    </TransitionGroup>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import Check from "./icons/Check.vue";
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
  modelValue: {
    type: Array,
    default: null,
  },
  max: {
    type: Number,
    default: 0,
  },
  mini: {
    type: Boolean,
    default: false,
  },
});

const animationFlag = ref(true);
const ANIMATION_DURATION = 400;

const durationStyle = computed(() => `transition-duration: ${ANIMATION_DURATION}ms`);

const emit = defineEmits(["update:modelValue"]);

const grid_ref = ref(null);
const wrapWidth = ref(0);

const elemWidth = computed(() => {
  const totalGap = props.gap * (props.perPage - 1);
  return (wrapWidth.value - totalGap) / props.perPage;
});

const testCheck = (id) => props.modelValue?.includes(id);

const pointerStyle = computed(() => (props.modelValue ? "cursor: pointer" : "cursor: default"));

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
    transform: `translate(${left}px, ${top}px)`,
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
const paddingStyle = computed(() => (!props.mini ? "padding:20px" : "padding:5px"));
const lineMarginStyle = computed(() => (!props.mini ? "margin:20px auto" : "margin:5px auto"));
const onClick = (id) => {
  if (props.modelValue && animationFlag.value) {
    animationFlag.value = false;
    setTimeout(() => {
      animationFlag.value = true;
    }, ANIMATION_DURATION);
    let newValue = props.modelValue ? [...props.modelValue] : [];
    if (newValue.includes(id)) {
      newValue = newValue.filter((item) => item !== id);
    } else {
      if (props.modelValue.length < props.max) {
        newValue.push(id);
      }
    }
    emit("update:modelValue", newValue);
  }
};
onMounted(() => {
  calcContainer();
  addEventListener("resize", () => {
    requestAnimationFrame(() => {
      calcContainer();
    });
  });
});
</script>

<style lang="scss" scoped>
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

    transition: transform ease;
    overflow: hidden;
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
    &__check {
      position: absolute;
      bottom: 14px;
      left: 50%;
      margin-left: -20px;
      transition: transform ease;
      transform: translateY(60px);
      &_checked {
        transform: translateY(0);
      }
    }
  }
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.fade-enter-to,
.fade-leave-from {
  opacity: 1;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}
</style>
