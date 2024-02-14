<script setup lang="ts">
import { computed, onBeforeMount, onBeforeUnmount, reactive, ref } from 'vue';
import { throttle } from 'throttle-debounce';
import SweetScroll from 'sweet-scroll';

const KEY_ENTER = 'ENTER';
const KEY_SPACE = 'SPACE';
const RIPPLE_DURATION = 750;
const THROTTLE_DELAY = 100;

const scroller = new SweetScroll();

interface Props {
  size?: number;
  right?: string | number;
  bottom?: string | number;
  bottomGap?: string | number;
  zIndex?: number;
  fgColor?: string;
  bgColor?: string;
  radius?: string | number;
  weight?: string;
  rippleBg?: string;
  boundary?: number;
  maxWidth?: number;
  src?: string | null;
  alt?: string;
  hasOutline?: boolean;
  boxShadow?: string;
  tabindex?: number;
}

const props = withDefaults(defineProps<Props>(), {
  size: 70,
  right: 50,
  bottom: 100,
  bottomGap: 0,
  zIndex: 1000,
  fgColor: '#ffffff',
  bgColor: '#ffc966',
  radius: '50%',
  weight: 'normal',
  rippleBg: 'rgba(255, 255, 255, .5)',
  boundary: 200,
  maxWidth: 640,
  src: null,
  alt: '',
  hasOutline: true,
  boxShadow: '1px 1px 2px rgba(0, 0, 0, .3)',
  tabindex: 0,
});

const enabled = ref(false);
const isActive = ref(false);
const isRippleActive = ref(false);
let throttleScroll: throttle<any> | null = null;
let throttleResize: throttle<any> | null = null

const outline = computed(() => props.hasOutline ? {} : { outline: '0 none' });

const fmtProp = (value: string | number, unit = 'px') => {
  return typeof value === 'number' ? value + unit : value;
};

const makeBgImage = (weight: string, fgColor: string) => {
  const color = fgColor.replace(/^#/, '');
  if (weight === 'bold') {
    return 'data:image/svg+xml;charset=utf8, %3Csvg version=%271.1%27 xmlns=%27http://www.w3.org/2000/svg%27 width=%2710px%27 height=%2710px%27%3E %3Cpolygon fill=%27%23' + color + '%27 stroke=%27none%27 points=%278.646,7.354 5,3.707 1.354,7.354 0.646,6.646 5,2.293 9.354,6.646%27 /%3E %3C/svg%3E';
  }
  return 'data:image/svg+xml;charset=utf8,%3Csvg version=%271.1%27 xmlns=%27http://www.w3.org/2000/svg%27 width=%2710px%27 height=%2710px%27%3E %3Cpolygon fill=%27%23' + color + '%27 stroke=%27none%27 points=%278.589,6.945 5,3.22 1.413,6.945 1.052,6.598 5,2.499 8.948,6.598%27 /%3E %3C/svg%3E';
};

const bgImage = () => {
  if (props.src !== null) {
    return 'none';
  }
  return `url("${makeBgImage(props.weight, props.fgColor)}")`;
};

const mainStyle = reactive({
  width: fmtProp(props.size),
  height: fmtProp(props.size),
  right: fmtProp(props.right),
  bottom: `calc(${fmtProp(props.bottom)} - ${fmtProp(props.bottomGap)})`,
  zIndex: props.zIndex,
  color: props.fgColor,
  backgroundColor: props.bgColor,
  backgroundImage: bgImage(),
  backgroundSize: `${props.size} auto`,
  boxShadow: props.boxShadow,
  borderRadius: fmtProp(props.radius),
  ...outline.value,
});

const rippleStyle = reactive({
  top: fmtProp(-props.size),
  left: fmtProp(-props.size),
  width: '200%',
  height: '200%',
  backgroundColor: props.rippleBg,
});

const clickHandle = (e: MouseEvent) => {
  if (!isRippleActive.value) {
    const elTarget = e.target as HTMLElement;
    if (elTarget) {
      const rect = elTarget.getBoundingClientRect();
      const top = e.clientY - rect.top;
      const left = e.clientX - rect.left;
      rippleStyle.top = fmtProp(top - props.size);
      rippleStyle.left = fmtProp(left - props.size);
    }
    isRippleActive.value = true;
    setTimeout(() => {
      isRippleActive.value = false;
    }, RIPPLE_DURATION);
  }
  scroller.to(0);
};

// toggle display by scrolling.
const handleScroll = () => {
  isActive.value = window.scrollY > props.boundary;
};

const handleResize = () => {
  if (props.maxWidth > 0) {
    enabled.value = !window.matchMedia(`(max-width: ${props.maxWidth}px)`).matches;
  } else {
    enabled.value = true;
  }
};

const keyEnter = (e: KeyboardEvent) => {
  if (e.key.toUpperCase() === KEY_ENTER || e.code.toUpperCase() === KEY_SPACE) {
    scroller.to(0);
  }
};

onBeforeMount(() => {
  handleResize();
  throttleScroll = throttle(THROTTLE_DELAY, handleScroll);
  throttleResize = throttle(THROTTLE_DELAY, handleResize);
  window.addEventListener('scroll', throttleScroll)
  window.addEventListener('resize', throttleResize)
});

onBeforeUnmount(() => {
  if (throttleScroll) {
    window.removeEventListener('scroll', throttleScroll);
  }
  if (throttleResize) {
    window.removeEventListener('resize', throttleResize);
  }
});

(() => {
})();
</script>

<template>
  <transition name="vue-go-top">
    <div
      v-if="isActive && enabled"
      :style="mainStyle"
      role="button"
      :tabindex="tabindex"
      class="vue-go-top"
      @click.prevent="clickHandle"
      @keydown.prevent="keyEnter"
    >
      <div
        v-if="src !== null"
        class="vue-go-top__icon"
      >
        <img
          :src="src"
          :alt="alt"
          class="vue-go-top__image"
        >
        <div
          :is-active="isRippleActive || null"
          :style="rippleStyle"
          class="vue-go-top__ripple"
        />
      </div>
      <div
        v-else
        class="vue-go-top__content"
      >
        <div
          :is-active="isRippleActive || null"
          :style="rippleStyle"
          class="vue-go-top__ripple"
        />
        <slot />
      </div>
    </div>
  </transition>
</template>

<style lang="scss" scoped>
.vue-go-top {
  overflow: hidden;
  position: fixed;
  cursor: pointer;
  background-repeat: no-repeat;
  background-position: 50% 50%;
  background-size: 70% auto;
  white-space: nowrap;
  text-indent: 100%;

  &__content {
    position: relative;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
  }

  &__ripple {
    position: absolute;
    border-radius: 50%;
    transform: scale(0);
    opacity: 0;
    pointer-events: none;

    &[is-active] {
      animation: vue-go-top-ripple-animation .75s ease-out;
    }
  }

  &__icon {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  &__image {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
}

.vue-go-top-enter-active,
.vue-go-top-leave-active {
  transition: opacity .3s, transform .3s;
}
.vue-go-top-enter-from,
.vue-go-top-leave-to {
  opacity: 0;
  transform: translateY(50px);
}

@keyframes vue-go-top-ripple-animation {
  from {
    opacity: 1;
  }
  to {
    transform: scale(2);
    opacity: 0;
  }
}
</style>
