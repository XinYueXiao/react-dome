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
-  添加plugins,安装插件[HtmlWebpackPlugin](https://webpack.js.org/plugins/html-webpack-plugin/#root "HtmlWebpackPlugin")

```javascript
+ var HtmlWebpackPlugin = require('html-webpack-plugin');
  const path = require('path');

  module.exports = {
    ......
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
     ]
 };
```
- 添加loader，并添加module

`yarn add babel-core@6.26.0 babel-preset-env@1.6.1 babel-loader@7.1.2 --dev`
```javascript
module.exports = {
...
module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env']
                    }
                }
            }
        ]
    }
...
```