# Vue-Lazyload

[![Build Status](https://img.shields.io/circleci/project/hilongjw/vue-lazyload/master.svg?style=flat-square)](https://circleci.com/gh/hilongjw/vue-lazyload)
[![npm version](https://img.shields.io/npm/v/vue-lazyload.svg?style=flat-square)](http://badge.fury.io/js/vue-lazyload)
[![npm downloads](https://img.shields.io/npm/dm/vue-lazyload.svg?style=flat-square)](http://badge.fury.io/js/vue-lazyload)
[![npm license](https://img.shields.io/npm/l/vue-lazyload.svg?style=flat-square)](http://badge.fury.io/js/vue-lazyload)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![CDNJS version](https://img.shields.io/cdnjs/v/vue-lazyload.svg)](https://cdnjs.com/libraries/vue-lazyload)

Vue模块，用于在应用程序中延迟加载图像。该项目的一些目标值得注意：

轻巧，功能强大且易于使用

处理任何图像类型

加载图像时添加加载类

## npm

```bash
1.安装插件

npm install vue-lazyload --save-dev

2.在入口文件main.js中引入并使用

import VueLazyload from 'vue-lazyload'

直接使用

Vue.use(VueLazyload)

或者添加自定义选项


Vue.use(VueLazyload, {  
  preLoad: 1.3,   //预加载的宽高比
  error: 'dist/error.png',//图片加载失败时使用的图片源
  loading: 'dist/loading.gif',//图片加载的路径
  attempt: 1//尝试加载次数
})

3.修改图片显示方式为懒加载（将 :src 属性直接改为v-lazy）

<a href="javascript:;"><img v-lazy="'/static/img/' + item.productImage"></a>

```

template:

```html
<ul>
  <li v-for="img in list">
    <img v-lazy="img.src" >
  </li>
</ul>
```

use `v-lazy-container` work with raw HTML

```html
<div v-lazy-container="{ selector: 'img' }">
  <img data-src="//domain.com/img1.jpg">
  <img data-src="//domain.com/img2.jpg">
  <img data-src="//domain.com/img3.jpg">  
</div>
```

custom `error` and `loading` placeholder image

```html
<div v-lazy-container="{ selector: 'img', error: 'xxx.jpg', loading: 'xxx.jpg' }">
  <img data-src="//domain.com/img1.jpg">
  <img data-src="//domain.com/img2.jpg">
  <img data-src="//domain.com/img3.jpg">  
</div>
```

```html
<div v-lazy-container="{ selector: 'img' }">
  <img data-src="//domain.com/img1.jpg" data-error="xxx.jpg">
  <img data-src="//domain.com/img2.jpg" data-loading="xxx.jpg">
  <img data-src="//domain.com/img3.jpg">  
</div>
```

## Constructor Options

|key|description|default|options|
|:---|---|---|---|
| `preLoad`|proportion of pre-loading height|`1.3`|`Number`|
|`error`|src of the image upon load fail|`'data-src'`|`String`
|`loading`|src of the image while loading|`'data-src'`|`String`|
|`attempt`|attempts count|`3`|`Number`|
|`listenEvents`|events that you want vue listen for|`['scroll', 'wheel', 'mousewheel', 'resize', 'animationend', 'transitionend', 'touchmove']`| [Desired Listen Events](#desired-listen-events) |
|`adapter`| dynamically modify the attribute of element |`{ }`| [Element Adapter](#element-adapter) |
|`filter`| the image's listener filter |`{ }`| [Image listener filter](#image-listener-filter) |
|`lazyComponent`| lazyload component | `false` | [Lazy Component](#lazy-component)
| `dispatchEvent`|trigger the dom event|`false`|`Boolean`|
| `throttleWait`|throttle wait|`200`|`Number`|
| `observer`|use IntersectionObserver|`false`|`Boolean`|
| `observerOptions`|IntersectionObserver options|{ rootMargin: '0px', threshold: 0.1 }|[IntersectionObserver](#intersectionobserver)|
| `silent`|do not print debug info|`true`|`Boolean`|

### Desired Listen Events

You can configure which events you want vue-lazyload by passing in an array
of listener names.

```javascript
Vue.use(VueLazyload, {
  preLoad: 1.3,
  error: 'dist/error.png',
  loading: 'dist/loading.gif',
  attempt: 1,
  // the default is ['scroll', 'wheel', 'mousewheel', 'resize', 'animationend', 'transitionend']
  listenEvents: [ 'scroll' ]
})
```

This is useful if you are having trouble with this plugin resetting itself to loading
when you have certain animations and transitions taking place


### Image listener filter

dynamically modify the src of image

```javascript
Vue.use(vueLazy, {
    filter: {
      progressive (listener, options) {
          const isCDN = /qiniudn.com/
          if (isCDN.test(listener.src)) {
              listener.el.setAttribute('lazy-progressive', 'true')
              listener.loading = listener.src + '?imageView2/1/w/10/h/10'
          }
      },
      webp (listener, options) {
          if (!options.supportWebp) return
          const isCDN = /qiniudn.com/
          if (isCDN.test(listener.src)) {
              listener.src += '?imageView2/2/format/webp'
          }
      }
    }
})
```


### Element Adapter

```javascript
Vue.use(vueLazy, {
    adapter: {
        loaded ({ bindType, el, naturalHeight, naturalWidth, $parent, src, loading, error, Init }) {
            // do something here
            // example for call LoadedHandler
            LoadedHandler(el)
        },
        loading (listender, Init) {
            console.log('loading')
        },
        error (listender, Init) {
            console.log('error')
        }
    }
})
```

### IntersectionObserver

use [Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to to improve performance of a large number of nodes.

```javascript
Vue.use(vueLazy, {
  // set observer to true
  observer: true,

  // optional
  observerOptions: {
    rootMargin: '0px',
    threshold: 0.1
  }
})
```


### Lazy Component
```javascript
Vue.use(VueLazyload, {
  lazyComponent: true
});
```

```html
<lazy-component @show="handler">
  <img class="mini-cover" :src="img.src" width="100%" height="400">
</lazy-component>

<script>
  {
    ...
    methods: {
      handler (component) {
        console.log('this component is showing')
      }
    }

  }
</script>
```


## Implementation

### Basic

vue-lazyload will set this img element's `src` with `imgUrl` string

```html
<script>
export default {
  data () {
    return {
      imgObj: {
        src: 'http://xx.com/logo.png',
        error: 'http://xx.com/error.png',
        loading: 'http://xx.com/loading-spin.svg'
      },
      imgUrl: 'http://xx.com/logo.png' // String
    }
  }
}
</script>

<template>
  <div ref="container">
     <img v-lazy="imgUrl"/>
     <div v-lazy:background-image="imgUrl"></div>

     <!-- with customer error and loading -->
     <img v-lazy="imgObj"/>
     <div v-lazy:background-image="imgObj"></div>

     <!-- Customer scrollable element -->
     <img v-lazy.container ="imgUrl"/>
     <div v-lazy:background-image.container="img"></div>

    <!-- srcset -->
    <img v-lazy="'img.400px.jpg'" data-srcset="img.400px.jpg 400w, img.800px.jpg 800w, img.1200px.jpg 1200w">
    <img v-lazy="imgUrl" :data-srcset="imgUrl' + '?size=400 400w, ' + imgUrl + ' ?size=800 800w, ' + imgUrl +'/1200.jpg 1200w'" />
  </div>
</template>
```

### CSS state

There are three states while img loading

`loading`  `loaded`  `error`

```html
<img src="imgUrl" lazy="loading">
<img src="imgUrl" lazy="loaded">
<img src="imgUrl" lazy="error">
```

```html
<style>
  img[lazy=loading] {
    /*your style here*/
  }
  img[lazy=error] {
    /*your style here*/
  }
  img[lazy=loaded] {
    /*your style here*/
  }
  /*
  or background-image
  */
  .yourclass[lazy=loading] {
    /*your style here*/
  }
  .yourclass[lazy=error] {
    /*your style here*/
  }
  .yourclass[lazy=loaded] {
    /*your style here*/
  }
</style>
```

## Methods

### Event Hook

`vm.$Lazyload.$on(event, callback)`
`vm.$Lazyload.$off(event, callback)`
`vm.$Lazyload.$once(event, callback)`

- `$on` Listen for a custom events `loading`, `loaded`, `error`
- `$once` Listen for a custom event, but only once. The listener will be removed once it triggers for the first time.
- `$off` Remove event listener(s).

#### `vm.$Lazyload.$on`

#### Arguments:

 * `{string} event`
 * `{Function} callback`

#### Example

```javascript
vm.$Lazyload.$on('loaded', function ({ bindType, el, naturalHeight, naturalWidth, $parent, src, loading, error }, formCache) {
  console.log(el, src)
})
```

#### `vm.$Lazyload.$once`

#### Arguments:

 * `{string} event`
 * `{Function} callback`

#### Example

```javascript
vm.$Lazyload.$once('loaded', function ({ el, src }) {
  console.log(el, src)
})
```

#### `vm.$Lazyload.$off`

If only the event is provided, remove all listeners for that event

#### Arguments:

 * `{string} event`
 * `{Function} callback`

#### Example

```javascript
function handler ({ el, src }, formCache) {
  console.log(el, src)
}
vm.$Lazyload.$on('loaded', handler)
vm.$Lazyload.$off('loaded', handler)
vm.$Lazyload.$off('loaded')
```

### LazyLoadHandler

`vm.$Lazyload.lazyLoadHandler`

Manually trigger lazy loading position calculation

#### Example

```javascript

this.$Lazyload.lazyLoadHandler()

```

### Performance

```javascript
this.$Lazyload.$on('loaded', function (listener) {
  console.table(this.$Lazyload.performance())
})
```

![performance-demo](http://ww1.sinaimg.cn/large/69402bf8gw1fbo62ocvlaj213k09w78w.jpg)

### Dynamic switching pictures

```vue
 <img v-lazy="lazyImg" :key="lazyImg.src">
```
