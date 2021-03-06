---
layout:     post
title:      "css3动画心电图"
date:       "2018-03-16 "
tags:       "css3 animation"
author:     "guozhaodong"
---

昨天一个前端群里的妹子问了一个问题，如何让一张图片一直往右滚动，我告诉她`css3`动画就可以做，而且很简单。过程中遇到了一些问题，先上代码

``` HTML
<div class="wave"></div>
```

``` CSS
.wave{
  width:500px;
  height:500px;
  background:url(https://bubble2.github.io/assets/img/css3-wave/9ca67e2ac65c103833dbb95db2119313b27e89fa.jpg) 0 center repeat-x;
  animation:animWave 2s infinite linear;
}

@keyframes animWave{
  0%{
    background-position:0 center;
  }
  100%{
    background-position:-500px center;
  }
  
}
```

<a href="https://codepen.io/Bubble2/pen/jzrKgm" target="_blank">demo点击此处</a>

主要实现原理就是，利用背景图片的横向平铺，然后修改背景图片的x轴坐标，一个动画周期移动的距离是背景图片的宽度，否则会出现卡顿的现象。

这样看起来已经很完美了，但是妹子忽然又说，我这个图片的大小不固定怎么办，我听了后说：容我想想。经过一翻思索尝试后，发现好像没有办法达到妹子的效果。只能如实告诉妹子这个没办法了。。。

后来妹子自己捣鼓出一个方法就是把这个背景图片x轴移动的距离和动画的时间方法几千倍，修改`css`代码


``` CSS
.wave{
  width:605px;
  height:404px;
  background:url(https://imgsa.baidu.com/forum/w%3D580/sign=4e45e1637cd98d1076d40c39113eb807/9ca67e2ac65c103833dbb95db2119313b27e89fa.jpg) 0 center repeat-x;
  animation:animWave 2000s infinite linear;
}

@keyframes animWave{
  0%{
    background-position:0 center;
  }
  100%{
    background-position:-605000px center;
  }
  
}
```

这个方法属于投机取巧型，但是正常情况下也是没有问题的。

--------

不过我一直想的是这个`background-position`是不是可以使用百分比来表示，这样的自由度会大点，尝试了几次发现对这个百分比有点陌生，后来专门去研究了一下这个东西，

对于 `background-position:x y`
如果这个`x`是`50%`，换算成`px`：(容器的宽度-图片的宽度)*50%；
如果这个`y`是`50%`，换算成`px`：(容器的高度-图片的高度)*50%；

这么一来如果，我背景图片位置设置成这样，`background-position:-100% center`,图片宽度如果是`605px`，那容器的宽度就是图片宽度的两倍即`1210px`

```HTML

<div class="wave-wrap">
  <div class="wave"></div>
</div>
```

``` CSS
.wave-wrap{
  width:605px;
  height:404px;
  border:1px solid #ddd;
  overflow:hidden;
}
.wave{
  width:1210px;
  height:404px;
  background:url(http://58pic.ooopic.com/58pic/14/65/06/10958PICvp4.jpg) 0 center repeat-x;
  animation:animWave 2s infinite linear;
}

@keyframes animWave{
  0%{
    background-position:0 center;
  }
  100%{
    background-position:-100% center;
  }
  
}
```

这样的话如果图片更换了，可以使用`js`去动态更改图片`url`、图片的尺寸、容器的尺寸。


















