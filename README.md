# react-dome
# react项目搭建过程
##  1.安装webpack 
`yarn add webpack --save`
-  新建 webpack.config.js
-  新建入口文件src/app.js
-  配置 webpack.config.js 入口及出口

```javascript
const path = require('path');

	module.exports = {
    entry: './src/app.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'app.js'
    }
};
```
-  执行 node_modules/.bin/webpack 即可在dist文件下查看app.js的打包文件
