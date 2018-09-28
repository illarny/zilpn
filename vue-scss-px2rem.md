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