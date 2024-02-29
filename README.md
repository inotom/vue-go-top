# vue-go-top

Scroll to page top button, Vue.js component.

From v2.0.0, it works for Vue 3.

## Demo

[Demo](https://sandbox.serendip.ws/vue-go-top.html)


## Install

### Browser

```html
<script src="vue.global.prod.js"></script>
<script src="https://unpkg.com/throttle-debounce@5/umd/index.js"></script>
<script src="https://unpkg.com/sweet-scroll/sweet-scroll.min.js"></script>
<script src="vue-go-top.umd.js"></script>
```


### npm

```
npm install --save @inotom/vue-go-top
```


## Usage

### Browser

```html
<div id="app">
  <go-top></go-top>
</div>

<script src="vue.global.prod.js"></script>
<script src="https://unpkg.com/throttle-debounce@5/umd/index.js"></script>
<script src="https://unpkg.com/sweet-scroll/sweet-scroll.min.js"></script>
<script src="vue-go-top.umd.js"></script>
<script>
const { createApp } = Vue;
const { GoTop } = SwsVueGoTop;

createApp({
  components: {
    GoTop,
  },
}).mount('#app');
</script>
```


### SFC (TypeScript)

```vue
<template>
  <go-top></go-top>
</template>

<script setup lang="ts">
import { GoTop } from '@inotom/vue-go-top';
</script>
```


## Props

| name           | type               | defaults                        | description                                 |
|----------------|--------------------|---------------------------------|---------------------------------------------|
| `:size`        | `Number`           | `70`                            | width/height pixel size                     |
| `:right`       | `String`, `Number` | `50`                            | margin from right                           |
| `:bottom`      | `String`, `Number` | `100`                           | margin from bottom                          |
| `:bottom-gap`  | `String`, `Number` | `0`                             | gap of bottom margin                        |
| `:z-index`     | `Number`           | `1000`                          | z-index size                                |
| `fg-color`     | `String`           | `#ffffff`                       | text color                                  |
| `bg-color`     | `String`           | `#ffc966`                       | background color                            |
| `:radius`      | `String`, `Number` | `50%`                           | border-radius size                          |
| `weight`       | `String`           | `normal`                        | line weight of arrow                        |
| `ripple-bg`    | `String`           | `rgba(255, 255, 255, .5)`       | ripple animation color                      |
| `:boundary`    | `Number`           | `200`                           | top position where the element is displayed |
| `:max-width`   | `Number`           | `640`                           | media query max-width(px)                   |
| `src`          | `String`           | `null`                          | image url                                   |
| `alt`          | `String`           | ``                              | alternative text for image                  |
| `:has-outline` | `Boolean`          | `true`                          | show outline when focus                     |
| `box-shadow`   | `String`           | `1px 1px 2px rgba(0, 0, 0, .3)` | box-shadow property                         |
| `:tabindex`    | `Number`           | `0`                             | tabindex property                           |


## License

MIT


## Author

inotom
