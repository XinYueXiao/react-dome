# react-dome
# react项目搭建过程
##  1.安装webpack 
`yarn add webpack --save`

### 1.1  新建 webpack.config.js、新建入口文件src/app.js、配置 webpack.config.js 入口及出口

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
### 1.2 执行 node_modules/.bin/webpack 即可在dist文件下查看app.js的打包文件
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
### 1.3 添加loader，并添加module

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

##2. react安装与配置
### 2.1 安装相关依赖

`yarn add babel-preset-react@6.24.1`

`yarn add react@16.2.0 react-dom@16.2.0`

### 2.2 修改文件 app.js-->app.jsx并添加配置文件

```javascript
import React from 'react';
import ReactDOM from 'react-dom'
ReactDOM.render(
    <h1>hello</h1>,
    document.getElementById('app')
)
```
- 修改webpack.config.js

````javascript
 entry: './src/app.jsx',//修改入口文件
 module: {
        rules: [
            {
                test: /\.jsx$/,//匹配规则由js->jsx
                exclude: /(node_modules)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env', 'react']//添加react
                    }
                }
            }
        ]
    },

````
- 重新打包` node_modules/.bin/webpack`，在浏览器中查看 dist/index.html

![页面访问](http://chuantu.xyz/t6/702/1570609470x3661913030.png "页面访问")

## 3. 添加样式解析配置webpack.config.js
### 3.1 配置css

`yarn add style-loader@0.19.1 css-loader@0.28.8 --dev`

```javascript
 module: {
        rules: [
			...,
			{//添加样式loader
                test: /\.css$/,
                use: [
                    'style-loader', 'css-loader',
                ]
            }
        ]
    }
```
新建 `src/index.css`，在app.jsx引入`import './index.css' `

`#app {
    color: pink;
}`

重新打包` node_modules/.bin/webpack`刷新页面

![添加样式打包](http://chuantu.xyz/t6/702/1570610071x3661913030.png "添加样式打包")

重新打包` node_modules/.bin/webpack`刷新页面

查看打包后文件css的渲染要等所有js执行完成才加载，则有一段等待时间，怎么解决这个问题

![css加载位置](http://chuantu.xyz/t6/702/1570610445x3703728804.png "css加载位置")

- 把css文件提取出来加载，添加依赖`yarn add extract-text-webpack-plugin@3.0.2  --dev`

 按照[官方配置](https://webpack.js.org/plugins/extract-text-webpack-plugin/#options "官方配置")修改`webpack.config.js`

```javascript
//引入
const ExtractTextPlugin = require("extract-text-webpack-plugin");
//替换module->css
 rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      }
    ]
 //添加plugins
 plugins: [
	 ...,
  +  new ExtractTextPlugin("index.css"),
  ]
```
重新打包` node_modules/.bin/webpack`，dist下新增了`index.css`文件，

添加了css引入`<link href="index.css" rel="stylesheet">`在浏览器中查看 dist/index.html样式已生效

### 3.2 配置sass
- 安装相关依赖 `yarn add sass-loader@6.0.6 node-sass@4.10.0 --dev`
- 配置sass

```javascript
//添加scss解析
 rules: [
	 ...,
       {
                test: /\.scss$/,
                use: ExtractTextPlugin.extract({
                    fallback: "style-loader",
                    use: ["css-loader", "sass-loader"]
                })
            }
    ]

```

- 新增`index.sass`， 在`index.jsx`引入sass，重新打包` node_modules/.bin/webpack`

```javascript
//index.scss
body {
    background: beige;

    #app {
        font-size: 100px;
    }
}
```
效果如下：

![sass](http://chuantu.xyz/t6/702/1570613235x3661913030.png "sass")

## 4.图片处理[url-loader](https://webpack.js.org/loaders/url-loader/#root "url-loader")
> 温馨提示：使用url-loader处理小图片比较方便，会自动处理成base64

- 安装相关依赖 `yarn add url-loader@0.6.2 file-loader@1.1.6 --dev`
- 添加图片处理配置

```javascript
 rules: [
	 ...,
	 	{
                test: /\.(png|jpg|gif)$/i,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 8192,
                        },
                    },
                ],
            },
	 ]
```
- 添加image引用

```javascript
body {
    background: url('./banner.jpg');
}
```
效果如下：

![image](http://chuantu.xyz/t6/702/1570615673x3661913030.png "image")

## 5. 添加字体库
- 添加字体库 `yarn add font-awesome` 并在 `app.jsx` 中引入 `font-awesome/css/font-awesome.min.css`

```javascript
import 'font-awesome/css/font-awesome.min.css'
ReactDOM.render(
    <div>
        <i className='fa fa-address-book'></i>
        <h1>hello</h1>
    </div>
    ,
    document.getElementById('app')
)

```
- 添加字体打包配置

```javascript
 rules: [
	 ...,
	 //字体图表的处理
            {
                test: /\.(woff|woff2|svg|ttf|eot|otf)$/i,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 8192,
                        },
                    },
                ],
            },
	 ]

```
 效果如下：
 
![icon](http://chuantu.xyz/t6/702/1570617093x3703728804.png "icon")

## 6. 提出公共模块分出文件类型
```javascript
var HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require("extract-text-webpack-plugin");
const path = require('path');
const webpack = require('webpack')

module.exports = {
    entry: './src/app.jsx',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'js/app.js'
    },
    module: {
        rules: [
            //react语法处理
            {
                test: /\.jsx$/,
                exclude: /(node_modules)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env', 'react']
                    }
                }
            },
            //css文件处理
            {
                test: /\.css$/,
                use: ExtractTextPlugin.extract({
                    fallback: "style-loader",
                    use: "css-loader"
                })
            },
            //sass文件处理
            {
                test: /\.scss$/,
                use: ExtractTextPlugin.extract({
                    fallback: "style-loader",
                    use: ["css-loader", "sass-loader"]
                })
            },
            //图片处理
            {
                test: /\.(png|jpg|gif)$/i,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 8192,
                            name: 'resourse/[name].[ext]'
                        },
                    },
                ],
            },
            //字体图表的处理
            {
                test: /\.(woff|woff2|svg|ttf|eot|otf)$/i,
                use: [
                    {
                        loader: 'url-loader',
                        options: {
                            limit: 8192,
                            name: 'resourse/[name].[ext]'
                        },
                    },
                ],
            },

        ]
    },
    plugins: [
        //处理html文件
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
        //独立css文件
        new ExtractTextPlugin("css/[name].css"),
        //提出公共模块
        new webpack.optimize.CommonsChunkPlugin({
            name: 'common',//手动指定的公共模块
            filename: 'js/base.js'
        })
    ]
};
```

