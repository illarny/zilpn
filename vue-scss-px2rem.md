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

### vue-loader.conf.js
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


### webpack.base.conf.js (部分)
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