---
layout:     post
title:      "webpack入门"
date:       "2018-12-25"
tags:        "webpack"
author:     "guozhaodong"
---


# webpack新手入门1

## 什么是webpack？

本质上，`webpack`是一个现代`javascript`应用程序的*静态模块打包器*，使用`webpack`后`js`是一等公民，所有资源都是通过它来引入的，由它来处理各种资源之间的依赖关系，最后会生成一个或多个打包好的文件。我们来看下官网的图：

![image](/assets/img/webpack/img1.jpg)

举个简单的栗子：

`src/add.js`
``` JavaScript
    import SelectGoods from './business/SelectGoods';

    //调用选择物品插件
    SelectGoods();
```

`src/business/SelectGoods.js`;
```JavaScript
    export default function SelectGoods(){
        console.log('This is a SelectGoods plugin')
    }
```

使用`webpack`打包

可以直接通过命令行，但是我们一般都是通过一个`webpack.config.js`配置文件

`webpack.config.js`

```JavaScript
    module.exports = {
        entry: './src/add.js',
        output: {
            filename: 'bundle.js',
            path: __dirname + 'dist'
        }
    }
```

在命令行中敲`webpack`就会生成一个`bundle.js`文件

`add.html`
``` Html
<!doctype html>
<html>
    <head>
    ...
    </head>
    <body>
    ...
    <script src="dist/bundle.js"></script>
    </body>
</html>
```

## webpack和我们熟悉的grunt、gulp有什么关系，又有什么区别？

`webpack`和`grunt`、`gulp`没有什么可比性，不过它可以完成后两者的很多功能。它是一种模块化的解决方案，而`grunt`和`gulp`是一种优化前端开发流程的工具。

`webpack`的工作方式：把你的项目当做一个整体，通过一个或多个给定的入口文件(如`index.js`)，`webpack`将从这个文件开始查找你项目中所有的依赖，最后打包成一个或者多个浏览器可以识别的`js`文件。

`grunt`和`gulp`的工作方式：在一个配置文件中，指明对某些文件进行类似编译、组合、压缩等任务的具体步骤，然后工具会自动替你完成这些指定的任务。


## 前端模块化

前面说到模块化解决方法，这里我们就再提下前端模块化，前端早期是没有模块化的说法的，但是随着技术的发展前端承载的东西越来越多，就出现了一个`js`页面几千行代码的情况，然后我们就会来拆分文件，随之就会出现两个大的问题，一是全局变量的污染，二是文件依赖的问题。为了解决这两个主要的问题，各路大神撸出了不少框架，比如`AMD`规范(`requirejs`)、`CMD`规范(`seajs`)、`CommonJs`规范(服务端`nodejs`的模块化规范，`webpack`能把`CommonJs`写出来的代码编译成浏览器能识别代码)、`ES6`的模块规范。

但是`webpack`又不仅仅是模块化方法，它提供了很多`loader`、`plugins`来帮助我们编译，打包我们的资源。所以综上可以看出`webpack`是一款功能十分强大的前端资源解决方案。

它特别适合目前流行的前后端分离的项目，本来我们需要使用`requireJs`+`grunt`，现在我们只需要使用`webpack`就可以解决。

## 核心概念

### 入口（entry）

应用的起点，我们会在`html`页面中用`script`标签引入的js文件

一般普通的应用，每一个`html`页面会有一个入口文件，单页应用(`SPA`)中我们一般只有一个入口文件。

配置文件会有几种方式：

#### 对象形式：

```JavaScript

//单入口起点
module.exports = {
    entry: {
        index: './src/index.js'
    },
    output: {
        filename: '[name].bundle.js'
    }
}
//打包后输出 index.bundle.js

//单入口起点
module.exports = {
    entry: {
        index: ['./src/index.js', './src/add.js']
    },
    output: {
        filename: '[name].bundle.js'
    }
}
//打包后输出 index.bundle.js

//多入口起点
module.exports = {
    entry: {
        index: './src/index.js',
        add: './src/add.js',
        show: './src/show.js'
    },
    output: {
        filename: '[name].bundle.js'
    }
}

//打包后输出 dist/index.js  dist/add.js  dist/show.js
```


#### 字符串形式：

```JavaScript

//单入口起点
module.exports = {
    entry: './src/index.js',
    output: {
        filename: '[name].bundle.js'
    }
}
//打包后输出 dist/main.js

//上面就等同于下面
module.exports = {
    entry: {
        main: './src/index.js'
    },
    output: {
        filename: '[name].bundle.js'
    }
}
//打包后输出 dist/main.js
```

#### 数组形式

```JavaScript

//单入口起点
module.exports = {
    entry: ['./src/index.js', './src/add.js'],
    output: {
        filename: '[name].bundle.js'
    }
}
//打包后输出 dist/main.js

//上面就等同于下面
module.exports = {
    entry: {
        main: ['./src/index.js', './src/add.js']
    },
    output: {
        filename: '[name].bundle.js'
    }
}
//打包后输出 dist/main.js
```

#### 函数形式

函数形式一般用于动态加载入口起点

``` JavaScript
module.exports = {
    entry: () => './src/ChartComponent.js'
}
```

> 总结下：字符串和数组的形式其实都可以用对象的形式来写，对象的形式也就那三种。

> 关于打包后输出文件的命名
> 1. 对象形式，对象的每一个`key`就是这个`bundle`的名称。
> 2. 字符串形式或者数组形式，`bundle`的名称就是默认的名称`main`。

[`entry`示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo1)

### 输出（output）

告诉`webpack`我们的`bundle`文件时如何输出的，下面我来介绍一下常用的配置项

#### filename: 输出的`bundle `名称，

可以是一个固定的名称，这种一般是针对但入口起点的

```JavaScript
module.exports = {
    output: {
        filename: 'bundle.js'
    }
}
```

如果是多入口起点会创建多个`bundle`，这个时候我们会指定一个动态的名称


可以是入口起点的名称

```JavaScript
module.exports = {
    entry: {
        index : './src/index.js'
    },
    output: {
        filename: '[name].bundle.js'
    }
}

//打包后输出 index.bundle.js
```

如果是生产环境我们还可以给它加一个`hash`值
```JavaScript
module.exports = {
    entry: {
        index: './src/index.js'
    },
    output: {
        filename: '[name].[hash].bundle.js'
    }
}
//打包后输出 index.8239442398343sj334s4.bundle.js
```

#### path

指定输出文件的`path`路径

``` JavaScript
module.exports = {
    entry: {
        index: './src/index.js',
    },
    output: {
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
}

//打包后输出 dist/index.bundle.js
```

[`output`示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo2)


### loader

`loader`用于对模块的源码进行转化，

它可以把不同的语言转换成浏览器能解析的`js`语言，或将图片转换成`data URL`，甚至允许你在`js`里直接`import Css`文件

比如我们熟悉的`babel-loader`用来把`es6`的代码以及`jsx`语法，转化成浏览器可以识别的`es5`代码。

下面我们来看下它是怎么使用的

#### module.rules

你可以在`rules`里面指定一个或多个`loader`

```JavaScript
module.exports = {
    entry: {
        index: './src/index.js'
    },
    output: {
        filename: '[name].bundle.js',
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets:['@babel/preset-env','@babel/preset-react']
                    }
                }
            },
            {
                test:/\.scss$/,
                use: [
                    'style-loader',
                    'css-loader',
                    'sass-loader'
                ]
                // use: [
                //     {
                //         loader: 'style-loader',
                //         options: {
                //             sourceMap: true
                //         }
                //     },
                //     {
                //         loader: 'css-loader',
                //         options: {
                //             sourceMap: true
                //         }
                //     },
                //     {
                //         loader: 'sass-loader',
                //         options: {
                //             sourceMap: true
                //         }
                //     }
                // ]
            }
        ]
    }
}
```

`module`下面的`rules`接受一个规则数组，也就是可以匹配多种资源

数组的每一项里面就代表对于一种规则类型的资源怎么去转化或者处理它，

条件规则：

`test`：匹配特定条件的资源，一般是一个正则表达式。

`include`：匹配特定条件的目录，用于缩小查找的范围，表示在该目录里面查找，一般是一个正则或者一个函数。

`exclude`：匹配特定条件的目录，用于缩小查找的范围，表示查找时排除该目录，一般是一个正则或者一个函数。

应用规则：

`use`：将我们匹配到的资源使用指定的`loader`，这边的会稍微复杂一点，书写的形式比较多，常用的形式有以下几种

```JavaScript

//一个loader，同时有options等其它属性
{
    test: /\.(png|jpg|gif|jpeg)$/,
    use: {
        loader: 'url-loader',
        options: {
            limit: 8192,
            name: '[name].[hash:8].[ext]'
        }
    }
}


//多个loader，同时有options等其它属性
{
    test:/\.scss$/,
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
                sourceMap: true
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

//一个或多个loader，没有其它属性
{
    test:/\.scss$/,
    use: ['style-loader', 'css-loader', 'sass-loader']
}

```

关于`loader`的具体配置请参考不同`loader`的配置文档。


[`loader`示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo3)


### 插件（plugins）

插件可以提供各种方式来自定义我们的构建过程，一般`loader`完成不了的都交给`plugins`来做，有时候也需要两者配合起来完成一个功能。

`webpack`内置了各种插件，同时社区中也有很多精选的插件可以供我们使用，如果还是满足不了我们的需求，也可以自己去写插件，当然这个需要我们对`webpack`的原理有深入地理解。

``` JavaScript
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    plugins: [
        new webpack.BannerPlugin('Hello World'),
        new HtmlWebpackPlugin({
            title: 'demo4',
            filename: 'index.html',
            template: 'src/index.html'
        })
    ]
}
```

上面使用了一个`BannerPlugin`，它是`webpack`内置的插件，用来在我们打包后的代码头部添加一些标记信息。


`html-webpack-plugin`是用来生成`html`页面的，在`webpack`里`js`是一级公民，所以`html`也是可以通过`js`来生成的，所有东西都依赖入口`js`。

[`plugins`示例代码点这里看](https://github.com/Bubble2/webpack4-share-demo/tree/master/demo4)






