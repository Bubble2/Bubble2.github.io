---
layout:     post
title:      "2018-01-08-better-css"
date:       "2018-01-08 "
author:     "guozhaodong"
---

### 一些`css`规范

#### 关于`css`属性值简写

1.`margin`、`padding`的简写

> 以下为`margin`的栗子，`padding`相同。

```
margin:10px 10px 10px 10px = margin:10px


margin:10px 5px 10px 5px = margin:10px 5px


margin:10px 5px 15px 5px = margin:10px 5px 15px

```

![image](/img/better-css/1.png)

上面的`padding`可以简写成
```
padding:21px 25px 0 28px;
```

2、十六进制颜色值的缩写

6位rgb颜色值如果两位两位相同的话可以简写成3位

```
#ffffff = #fff

#ff00ee = #f0e
```

![image](/img/better-css/2.png)

#### 尽量去使用通用的东西，不要重复造轮子

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
