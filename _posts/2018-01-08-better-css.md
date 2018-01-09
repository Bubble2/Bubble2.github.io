---
layout:     post
title:      "2018-01-08-better-css"
date:       "2018-01-08 "
author:     "guozhaodong"
---

### 一些`css`规范

#### 一、关于`css`属性值简写

1.`margin`、`padding`的简写

> 以下为`margin`的栗子，`padding`相同。

``` css
margin:10px 10px 10px 10px = margin:10px

margin:10px 5px 10px 5px = margin:10px 5px

margin:10px 5px 15px 5px = margin:10px 5px 15px

```

![image](/img/better-css/1.png)

上面的`padding`可以简写成

``` css
padding:21px 25px 0 28px;
```

2、十六进制颜色值的缩写

6位rgb颜色值如果两位两位相同的话可以简写成3位

``` css
#ffffff = #fff

#ff00ee = #f0e
```

![image](/img/better-css/2.png)

#### 二、尽量去使用通用的东西，不要重复造轮子

1、如按钮(`ui_button`)、提示(`ui_tip`)、弹框(`ui_pop`)、图标(`ui_icon`)等都有现成的组件,如果没有特殊情况都可以使用。

![image](/img/better-css/3.png)

上面的栗子可以把`btn bulletin-more`改成`btn btn-primary btn-xl`

2、sass中也有一些公共的方法（`mixin`，`function`等）

![image](/img/better-css/4.png)

上面的文字超出指定宽度出现省略号的效果也有现成的`mixin`

``` css
@mixin textclip($maxWidth){
    max-width:$maxWidth;
    overflow:hidden;
    white-space:nowrap;
    text-overflow:ellipsis;
}

//可以这么使用
h3{
    font-size:16px;
    margin-bottom:-4px;
    @include textclip(100%);
}

```

#### 三、关于`class`的命名

`class`的命名规范比较多，仁者见仁智者见智，而且不同的开发团队对于规范也是不尽相同，

##### 命名原则：

（1）尽量精简、明确；

（2）必须以字母开头命名,且所有字母均为小写，一般使用英文单词或者缩写；

（3）单词之间使用中划线`-`连接；

（4）避免使用`left`、`right`等位置信息去命名

![image](/img/better-css/6.png)

（5）避免使用中文拼音去命名

![image](/img/better-css/7.png)

##### 命名方法

基类-继承类-其它类

``` html
 <div class="floor">
    <div class="floor-hd">
        <h3 class="floor-tit">楼层标题</h3>
        <span class="floor-tail"></span>
    </div>
    <div class="floor-bd">
        <div class="pro-lst">
            <ul>
                <li>
                    <div class="pro-info">
                        <p class="pro-tit">产品名称</p>
                        <p class="pro-price">￥13.00</p>
                    </div>
                    <div class="pro-img">
                        <img src="">
                    </div>
                </li>
            </ul>
        <div>
    </div>
 </div>
```

下面再讲下命名的几个注意点吧。

1、如果当前写的样式是一个通用的样式，很多页面会引用到，那么你的`class`命名要比较特殊、复杂一点，这样做可以尽量防止页面其它`class`与它命名冲突。

举个栗子：
![image](/img/better-css/5.png)

上面这个编辑浮层`class`为`widget-editable-layer`采用了三个单词进行连接，也可以尝试一下使用`BEM`方式命名。

2、如果当前写的样式是具体某个页面才会用到的，那么你只要保证在当前页面是唯一的就行了，所以命名可以稍微简单一些。

