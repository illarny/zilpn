# vue2 + scss + px2rem


### 安装
```bash
vue-init webpack pxrem
cd pxrem
npm install postcss-loader
npm install postcss-px2rem
npm install lib-flexible
npm install sass-loader -D
npm install node-sass -D
```


### build/vue-loader.conf.js
```js
'use strict'
const utils = require('./utils')
const config = require('../config')
const isProduction = process.env.NODE_ENV === 'production'
const sourceMapEnabled = isProduction
  ? config.build.productionSourceMap
  : config.dev.cssSourceMap

module.exports = {
  postcss: [require('postcss-px2rem')({remUnit: 75})],
  loaders: utils.cssLoaders({
    sourceMap: sourceMapEnabled,
    extract: isProduction
  }),
  cssSourceMap: sourceMapEnabled,
  cacheBusting: config.dev.cacheBusting,
  transformToRequire: {
    video: ['src', 'poster'],
    source: 'src',
    img: 'src',
    image: 'xlink:href'
  }
}
```


### build/webpack.base.conf.js (部分)
```js
...
module.exports = {
  ...
  module: {
    rules: [
      ...(config.dev.useEslint ? [createLintingRule()] : []),
      ...
      {
        test: /\.(css|scss)$/,
        loader:"style-loader!css-loader!sass-loader!postcss-loader"
      }
      ...
    ]
  },
  ...
}
```

### src/main.js
```js
import Vue from 'vue'
import App from './App'
import router from './router'
import 'lib-flexible'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```


### src/App.vue
```vue
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style lang="scss">
</style>

```


### index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <!-- <meta name="viewport" content="width=device-width,initial-scale=1.0"> -->
    <title>pxrem</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```


### src/components/Helloworld.vue
```vue
<template>
  <div class="hello">
    <div class="img">111</div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
    }
  }
}
</script>

<style lang="scss" scoped>
  .img{
    font-size: 24px; /*px*/
    width: 200px;
    height: 200px;
    background: #f00;
    border: 1px solid #000; /*no*/
  }
</style>
```