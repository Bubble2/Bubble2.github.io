---
layout: post
title: 'webpack实战'
date: '2018-12-25'
tags: 'webpack'
author: 'guozhaodong'
---

# webpack 新手入门-实战篇

> 下面实例版本为`webpack 4.x`

## 常用`loader`使用介绍

### `babel-loader`

#### 作用

主要用来把我们`js`中的`es6`，`jsx`等语法转换成浏览器能够执行的`js`代码。

#### 安装

```JavaScript
npm install babel-loader @babel/core @babel/preset-env @babel/preset-react --save-dev
```

#### 用法

```JavaScript
module:{
    rules:[
        {
            test: /\.(js|jsx)$/,
            exclude: /node_modules/,
            use: {
                loader: 'babel-loader', //主要用于编译es6语法和react的jsx语法
                options: {
                    cacheDirectory: true, //开启缓存，提升速度
                    presets: ['@babel/preset-react']
                }
                //options请看.babelrc文件
            }
        }
    ]
}
```

如果`babel`的配置选项比较多，你也可以把它放到`.babelrc`文件下

```JavaScript
{
    "presets": [
        [
            "@babel/preset-env",  //根据配置的目标浏览器或者运行环境来自动转换es6代码
        ],
        "@babel/preset-react"  //转换jsx语法
    ],
    "plugins": [
        "react-hot-loader/babel"
    ]
}
```

[示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo5)

### `scss-loader`、`css-loader`、`style-loader`

#### 作用

`scss-loader`用来把`scss`代码编译成`css`代码，

`css-loader`主要用来处理`css`中的`@import/url()`

`style-loader`主要用来把`css`代码通过内联样式插入到`html`文件中

#### 安装

```JavaScript
npm install sass-loader node-sass css-loader style-loader --save-dev
```

> 注意这边`sass-loader`依赖`node-sass`

#### 用法

```JavaScript
module: {
    rules: [
        {
            test: /\.scss$/,
            exclude: /node_modules/,
            use: [
                {
                    loader: 'style-loader',
                    options: {
                        sourceMap: true
                    }
                },
                {
                    loader: 'css-loader',
                    options: {
                        importLoaders: 1, //使用import之前还要经过几次loader(如果css中没用到@import，可以不加)
                        sourceMap: true,
                        modules: true, //启用css modules
                        localIdentName: '[local]--[hash:base64:5]',
                    }
                },
                {
                    loader: 'sass-loader',
                    options: {
                        sourceMap: true
                    }
                }
            ]
        }
    ]
}
```

[示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo6)

## 插件

### [`html-webpack-plugin`](https://github.com/jantimon/html-webpack-plugin)

#### 作用

创建一个 `html` 文件，对于在文件名中包含每次会随着编译而发生变化哈希的静态资源很有用。

#### 安装

```JavaScript
npm install --save-dev html-webpack-plugin
```

#### 用法

```JavaScript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');

plugins: [
    new HtmlWebpackPlugin({
        title: '百卓优采云进销存软件',
        filename: 'index.html',
        template: path.resolve(__dirname, 'public/index.html'),
        favicon: path.resolve(__dirname, 'public/favicon.ico')
    })
]
```

[示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo7)

### [`mini-css-extract-plugin`](https://github.com/webpack-contrib/mini-css-extract-plugin)

#### 作用

提取`css`代码为一个单独的文件

#### 安装

```JavaScript
npm install --save-dev mini-css-extract-plugin
```

#### 用法

```JavaScript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

plugins: [
    new MiniCssExtractPlugin({
        filename: "[name].css",
        chunkFilename: "[id].css"
    })
],
module: {
    rules: [
        {
            test: /\.scss$/,
            exclude: /node_modules/,
            use: [
                {
                    loader: MiniCssExtractPlugin.loader,
                    options: {
                        publicPath: '../' //css中引入背景图片会在图片url前面加上该路径
                    }
                },
                {
                    loader: 'css-loader',
                    options: {
                        importLoaders: 1, //使用import之前还要经过几次loader
                        modules: true,
                        localIdentName: '[local]--[hash:base64:5]',
                    }
                },
                {
                    loader: 'sass-loader'
                }
            ]
        },
    ]
}
```

[示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo8)

## 本地服务

开发环境下我们需要一个本地服务器，并且我们希望它支持热加载(hmr),`webpack-dev-server`提供了这个功能，

```JavaScript
devServer: {
    port: '9000',
    compress: true, //是否启用gzip压缩
    disableHostCheck: true, //绕过主机检查
    host: '0.0.0.0', //如果你希望服务器外部可访问
    // hot: true, // 模块热替换,必须要配合webpack.HotModuleReplacementPlugin插件使用
    historyApiFallback: true, //当使用 HTML5 History API 时，任意的 404 响应都可能需要被替代为 index.html
    publicPath: '/',  //用来本地服务拦截带publicPath开头的请求的，和output的publicPath一致
	contentBase: '/' //用来指定被访问html页面所在目录的
    ...
},

```

[示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo9)

## 下面我们来通过`webpack`搭建一个`react`的构建环境

构建环境一般需要考虑到开发环境和生产环境，两者的侧重点不同。

开发环境主要需要给开发者提供一个本地服务，同时能够具备热加载，源码调试的功能；

而生产环境需要对资源进行合理化地拆分和压缩处理来提升静态资源的加载速度。

所以我们需要针对两者进行一些差异化的配置。

---

一般我们会有两种方式来进行构建，一种方式是提供一个通用的配置文件`base`，然后针对不同构建环境再配置。

```JavaScript
//webpack.base.config.js
module.exports = {
    ...
}

//webpack.dev.config.js
module.exports = {
    mode: 'development'
}

//webpack.prod.config.js
module.exports = {
    mode: 'production'
}

```

还有一种方式是开发环境和生产环境的配置都用同一份配置文件，然后在配置文件中通过参数判断是生产环境还是开发环境再进行差异化的配置。

```JavaScript
module.exports = function(env, argv){
    return {
        mode: env.production ? 'production' : 'development',
        devtool: env.production ? 'source-maps' : 'eval',
    }
}
```

我这边采用了第一种方式来进行配置

`webpack.base.config.js`

```JavaScript
const HtmlWebpackPlugin = require('html-webpack-plugin');
const path = require('path');

module.exports = {
	entry: {
		index: path.resolve(__dirname, 'src/index.js')
	},
	module: {
		rules: [
			{
				test: /\.(js|jsx)$/,
				exclude: /node_modules/,
				use: {
					loader: 'babel-loader', //主要用于编译es6语法和react的jsx语法
					query: {
						cacheDirectory: true //开启缓存，提升速度
					}
					//options请看.babelrc文件
				}
			}
		]
	},
	plugins: [
		new HtmlWebpackPlugin({
			title: '百卓优采云进销存软件',
			filename: 'index.html',
			template: path.resolve(__dirname, 'public/index.html'),
			favicon: path.resolve(__dirname, 'public/favicon.ico')
		})
	],
	resolve: {
		extensions: ['.js', '.jsx'],
		alias: {
			components: path.resolve(__dirname, 'src/components'),
			styles: path.resolve(__dirname, 'src/styles'),
			images: path.resolve(__dirname, 'src/images')
		}
	}
};

```

`webpack.dev.config.js`

```JavaScript
const webpack = require('webpack');
const webpackMerge = require('webpack-merge');
const path = require('path');
const webpackBaseConfig = require('./webpack.base.config');

module.exports = webpackMerge(webpackBaseConfig, {
	mode: 'development',
	output: {
		filename: '[name].js'
	},
	devtool: 'cheap-module-eval-source-map',
	devServer: {
		port: 9000,
		compress: true,
		disableHostCheck: true,
		host: '0.0.0.0',
		hot: true,
		historyApiFallback: true,
		publicPath: '/'
	},
	module: {
		rules: [
			{
				test: /\.scss$/,
				exclude: /node_modules/,
				use: [
					{
						loader: 'style-loader',
						options: {
							sourceMap: true
						}
					},
					{
						loader: 'css-loader',
						options: {
							importLoaders: 1, //使用import之前还要经过几次loader
							sourceMap: true,
							modules: true,
							localIdentName: '[local]--[hash:base64:5]'
						}
					},
					{
						loader: 'sass-loader',
						options: {
							sourceMap: true
						}
					}
				]
			},
			{
				test: /\.(jpe?g|png|gif|svg)$/,
				use: {
					loader: 'file-loader',
					options: {
						name: '[name].[ext]'
					}
				}
			}
		]
	},
	plugins: [new webpack.HotModuleReplacementPlugin()]
});

```

`webpack.prod.config.js`

```JavaScript
const webpack = require('webpack');
const webpackMerge = require('webpack-merge');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const path = require('path');
const webpackBaseConfig = require('./webpack.base.config');

module.exports = webpackMerge(webpackBaseConfig, {
	mode: 'production',
	output: {
		filename: 'js/[name].[chunkhash:8].js',
		chunkFilename: 'js/[name].[chunkhash:8].js',
		path: path.resolve(__dirname, 'dist'),
		publicPath: '//jxc.abiz.com/'
	},
	module: {
		rules: [
			{
				test: /\.scss$/,
				exclude: /node_modules/,
				use: [
					{
						loader: MiniCssExtractPlugin.loader,
						options: {
							publicPath: '../' //css中引入背景图片会在图片url前面加上该路径
						}
					},
					{
						loader: 'css-loader',
						options: {
							importLoaders: 1, //使用import之前还要经过几次loader
							modules: true,
							localIdentName: '[local]--[hash:base64:5]'
						}
					},
					{
						loader: 'sass-loader'
					}
				]
			},
			{
				test: /\.(jpe?g|png|gif|svg)$/,
				use: {
					loader: 'url-loader',
					options: {
						limit: 8192,
						outputPath: 'images/',
						name: '[name].[hash:8].[ext]'
					}
				}
			}
		]
	},
	plugins: [
		//清除dist目录文件
		new CleanWebpackPlugin(),
		//提取css为单独css文件
		new MiniCssExtractPlugin({
			filename: 'css/[name].[contenthash:8].css',
			chunkFilename: 'css/[name].[contenthash:8].css'
		})
	],
	optimization: {
		minimizer: [new UglifyJsPlugin({}), new OptimizeCSSAssetsPlugin({})]
	},
	resolve: {
		modules: [
			path.resolve(__dirname, 'src'),
			path.resolve(__dirname, 'node_modules')
		]
	}
});

```

[示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo10)

掌握以上的配置，你就可以通过`webpack`完成一个基本的项目构建配置，如果你希望了解一些高级的配置，如构建的性能优化，资源的分包优化，甚至一些更高级的，比如自己去写一个`webpack`插件，可以去官网了解更多[英文](https://webpack.js.org/) [中文](https://www.webpackjs.com/)
