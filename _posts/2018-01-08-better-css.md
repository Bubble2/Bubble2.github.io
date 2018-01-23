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

（4）避免使用方位（`left`、`right`）、颜色（`red`）等单词去组合命名（除非是单纯地原子样式）

![image](/img/better-css/6.png)

（5）避免使用中文拼音去命名

![image](/img/better-css/7.png)

下面再讲下命名的几个注意点吧。

1、如果当前写的样式是一个通用的样式，很多页面会引用到，那么你的`class`命名要比较特殊、复杂一点，这样做可以尽量防止页面其它`class`与它命名冲突。如果当前写的样式是具体某个页面才会用到的，那么你只要保证在当前页面是唯一的就行了，所以命名可以稍微简单一些。

举个栗子：
![image](/img/better-css/5.png)

上面这个编辑浮层`class`为`widget-editable-layer`采用了三个单词进行连接，也可以尝试一下使用`BEM`方式命名。

#### 四、关于属性名的嵌套层级不宜太多

不论是`css`还是`sass`中的属性名称正常情况下不要超过三个层级
特别是`sass`可以属性嵌套，很多人都喜欢一层套一层，最后套了很多层

![image](/img/better-css/8.png)

``` scss
//bad

.widget-floor-tp4-style4{
    .pro-lst{
        ul{
            li{
                .txt-top{
                    .title{

                    }
                }
            }
        }
    }
}

//good

.widget-floor-tp4-style4{
    .pro-lst{
        ul{

        }
        li{

        }
        .txt-top{
            
        }
        .title{

        }
    }
}
```

### 如何更好地去书写`css`

#### 一、关于`css`属性的书写

1、对于一个`class`需要写哪些`css`属性，有的人可能会搞不太清楚,下面我来把基本的属性罗列一下；书写的顺序最好也按照下面的这种顺序去写。

``` css
    .box{
        /*定位相关属性*/
        position:absolute;
        top:0;
        right:0;
        bottom:0;
        left:0;
        z-index:10;
        ...

        /*布局相关属性*/
        display:block;
        float:left;
        ...

        /*自身属性*/
        width:100px;
        height:100px;
        line-height:100px;
        padding:10px;
        margin:10px;
        ...

        /*文本相关属性*/
        font-family:Arial;
        font-size:14px;
        font-style:normal;
        color:#000;
        text-align:center;
        ...

        /*视觉效果相关*/
        background:#f5f5f5;
        border:1px solid #e5e5e5;
        border-radius:5px;
        box-shadow:0 0 10px rgba(0,0,0,1);
        ...

        /*其它属性*/
        opacity:0;
        overflow:hidden;
        transition:.2s ease;
    }
```

#### 二、关于`margin`和`padding`的使用

![image](/img/better-css/9.png)

这个空白我到底是用`margin`还是`padding`？加在哪个div上面呢？

先看下`html`结构

``` html
    <div class="widget-floor-tp3-style1">
    
    </div>
    <div class="widget-floor-tp3-style2">
  
    </div>
    <div class="widget-floor-tp3-style3">
    
    </div>
```

如果这个边距全部用下外边距或者上外边距，如图10
``` css
    .widget-floor-tp3-style1{
        margin-bottom:40px;
    }
    .widget-floor-tp3-style2{
        margin-bottom:40px;
    }
    .widget-floor-tp3-style3{
        margin-bottom:40px;
    }
```

![image](/img/better-css/10.png)

图10

这样做存在的问题是：第一个楼层就没有上边距了，最后一个楼层下边距过大；同时如果有楼层有背景色，`margin`部分就不会包含背景色。

如果上下边距都加`margin`，那么会存在外边距合并现象。

那么只能使用`padding`来实现，如图11

``` css
    .widget-floor-tp3-style1{
        padding:20px 0;
    }
    .widget-floor-tp3-style2{
        padding:20px 0;
    }
    .widget-floor-tp3-style3{
        padding:20px 0;
    }

``` 

![image](/img/better-css/11.png)
图11

这样做好像已经达到我们的效果了，再想想看会不会有其它情况出现。

如果楼层添加了文字模块作为标题，那么标题和下面内容的间距是不是太大了点，如图12

![image](/img/better-css/12.png)
图12

那么我们可以把上边距调小，下边距增大，如图13

``` css
    .widget-floor-tp3-style1{
        padding:10px 0 30px;
    }
    .widget-floor-tp3-style2{
        padding:10px 0 30px;
    }
    .widget-floor-tp3-style3{
        padding:10px 0 30px;
    }

``` 

![image](/img/better-css/13.png)
图13

> 正常情况下空白建议首先考虑使用padding去填充。其次我们在使用的时候需要多去考虑其场景的变化，看看我们的做的是否适合能考虑到的所有场景。

#### 三、布局时候尽量考虑自适应

布局的时候要考虑到此处是否会经常修改或变化，如果是则尽量做到自适应,这样后期的修改就会少点。

![image](/img/better-css/14.png)

``` css
    //bad

    {
        position:absolute;
        top:16px;
        width:70px;
        height:10px;
    }

    //good

    {
        position:absolute;
        top:50%;
        width:70px;
        height:10px;
        margin-top:-10px;
    }
```

#### 四、负`margin`的运用

1、影响元素的宽度

如果元素<b>不脱离文档流</b>、<b>不使用`float`</b>、`width`为`auto`的话，给元素添加`margin-left`或者`margin-right`为负值时，都会增加元素自身的宽度。

运用场景 <a href="https://codepen.io/Bubble2/pen/WdWrPP" target="_blank">栗子点我</a>

![image](/img/better-css/15.png)

![image](/img/better-css/16.png)








