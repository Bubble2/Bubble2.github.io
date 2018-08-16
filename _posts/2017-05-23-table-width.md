---
layout:     post
title:      "表格宽度计算规则"
date:       "2017-05-23"
tags:       "css"
author:     "guozhaodong"
---

<style>
    .table-wrap{width:100%;overflow:auto;}
    .table-wrap .tb-fixed{table-layout:fixed;}
    .table-wrap table{width:100%;border-collapse:collapse;}
    .table-wrap th,.table-wrap td{line-height:18px;padding:6px 5px;border:1px solid #ccc;text-align:center;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
    .table-wrap th{background:#efefef;}
    .table-wrap td{background:#fff;}
    /*demo2*/
    .tb-demo2 .col1{width:50px;}
    .tb-demo2 .col2{width:50px;}
    .tb-demo2 .col3{width:50px;}
    .tb-demo2 .col4{width:50px;}
    .tb-demo2 .col5{width:50px;}
    .tb-demo2 .col6{width:50px;}
    .tb-demo2 .col7{width:50px;}
    .tb-demo2 .col8{width:50px;}
    .tb-demo2 .col9{width:50px;}
    .tb-demo2 .col10{width:50px;}
    /*demo3*/
    .tb-demo3 .col1{width:150px;}
    .tb-demo3 .col2{width:150px;}
    .tb-demo3 .col3{width:150px;}
    .tb-demo3 .col4{width:150px;}
    .tb-demo3 .col5{width:150px;}
    .tb-demo3 .col6{width:150px;}
    .tb-demo3 .col7{width:150px;}
    .tb-demo3 .col8{width:110px;}
    .tb-demo3 .col9{width:110px;}
    .tb-demo3 .col10{width:120px;}
    /*demo4*/
    .tb-demo4 .col1{width:150px;background:#999;}
    .tb-demo4 .col2{width:150px;background:#999;}
    .tb-demo4 .col3{width:150px;background:#999;}
    .tb-demo4 .col4{width:150px;background:#999;}
    /*demo5*/
    .tb-demo5 .col1{width:150px;background:#999;}
    .tb-demo5 .col2{width:150px;background:#999;}
    .tb-demo5 .col3{width:150px;background:#999;}
    .tb-demo5 .col4{width:150px;background:#999;}
    .tb-demo5 .col5{width:150px;background:#999;}
    .tb-demo5 .col6{width:150px;background:#999;}
    .tb-demo5 .col7{width:250px;background:#999;}

    /*demo7*/
    .tb-demo7 .col1{min-width:50px;}
    .tb-demo7 .col2{min-width:50px;}
    .tb-demo7 .col3{min-width:50px;}
    .tb-demo7 .col4{min-width:50px;}
    .tb-demo7 .col5{min-width:50px;}
    .tb-demo7 .col6{min-width:50px;}
    .tb-demo7 .col7{min-width:50px;}
    .tb-demo7 .col8{min-width:50px;}
    .tb-demo7 .col9{min-width:50px;}
    .tb-demo7 .col10{min-width:50px;}

    /*demo8*/
    .tb-demo8 .col1{min-width:50px;}
    .tb-demo8 .col2{min-width:50px;}
    .tb-demo8 .col3{min-width:50px;}
    .tb-demo8 .col4{min-width:50px;}
    .tb-demo8 .col5{min-width:50px;}
    .tb-demo8 .col6{min-width:50px;}
    .tb-demo8 .col7{min-width:50px;}
    .tb-demo8 .col8{min-width:50px;}
    .tb-demo8 .col9{min-width:50px;}
    .tb-demo8 .col10{min-width:50px;}

    /*demo9*/
    .tb-demo9 .col1{min-width:50px;background:#999;}
    .tb-demo9 .col2{min-width:50px;background:#999;}
    .tb-demo9 .col7{min-width:50px;background:#999;}
    .tb-demo9 .col8{min-width:50px;background:#999;}
    .tb-demo9 .col10{min-width:50px;background:#999;}

    /*demo10*/
    .tb-demo10 .col1{min-width:50px;background:#999;}
    .tb-demo10 .col2{min-width:50px;background:#999;}
    .tb-demo10 .col7{min-width:50px;background:#999;}
    .tb-demo10 .col8{min-width:50px;background:#999;}
    .tb-demo10 .col10{min-width:50px;background:#999;}
</style>

# 关于html表格单元格宽度的计算规则

## 关于表格宽度的渲染规则

表格单元格宽度的计算方式主要分为两种方式：**固定表格布局**、**自动表格布局**，这个经常写css的人应该还是知道的，但是我们经常会发现给表格列定了宽度不起作用，又或是没有定宽度渲染出来的却是正常的吗，下面就来介绍下这两个方式具体是怎么计算渲染的。

先设定几个通用的变量：

- tableWidth=表格宽度=100%
- tableBorderWidth=表格左右边框宽度
- tdBorderWidth=所有列左右边框宽度和（合并的边框算1px）
- tdPadding=所有列左右内填补和
- tdWidth=所有定义了width的列的宽度和
- tdLength=列个数

### 一、固定表格布局，表格添加`table-layout:fixed`

> ps：在固定表格布局中，表格列的宽度与列内容多少无关，只与表格宽度、列宽度、表格左右边框、列左右边框、列左右内填补有关

通过使用固定表格布局，用户代理在接收到第一行后就可以显示表格，即只有第一行的宽度才会起作用

`width`为`auto`的列的宽度（即未定义`width`的列的宽度,如果计算结果为负数则为0）= `(tableWidth-tableBorderWidth-tdBorderWidth-tdPadding-tdWidth)/tdLength`

#### 1、所有th宽度未定义

>  每列的宽度通过表格宽度平均分配

<div class="table-wrap">
<table class="tb tb-demo1 tb-fixed">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1row1row1row1row1row1row1row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5row5row5row5row5row5row5row5</td>
<td class="col6">row6</td>
<td class="col7">row7row7row7row7row7row7row7row7</td>
<td class="col8">row8row8row8row8row8row8row8row8</td>
<td class="col9">row9row9row9row9row9row9row9row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

#### 2、所有th都定了宽度，同时所有列宽度之和小于表格宽度（tableBorderWidth+tdBorderWidth+tdPadding+tdWidth <= tableWidth）

> 每列的宽度通过总宽度平均分配；表格的宽度为其定义的宽度

<div class="table-wrap">
<table class="tb tb-demo2 tb-fixed">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1row1row1row1row1row1row1row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5row5row5row5row5row5row5row5</td>
<td class="col6">row6</td>
<td class="col7">row7row7row7row7row7row7row7row7</td>
<td class="col8">row8row8row8row8row8row8row8row8</td>
<td class="col9">row9row9row9row9row9row9row9row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

#### 3、所有th都定了宽度，同时所有列宽度之和大于表格宽度（tableBorderWidth+tdBorderWidth+tdPadding+tdWidth > tableWidth）

> 每列的宽度为自身定义的宽度;表格的宽度为所有列宽度总和（会超出表格定义的宽度）
<div class="table-wrap">
<table class="tb tb-demo3 tb-fixed">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1row1row1row1row1row1row1row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5row5row5row5row5row5row5row5</td>
<td class="col6">row6</td>
<td class="col7">row7row7row7row7row7row7row7row7</td>
<td class="col8">row8row8row8row8row8row8row8row8</td>
<td class="col9">row9row9row9row9row9row9row9row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

#### 4、部分th定了宽度，同时定了th宽度的列的宽度之后小于表格宽度（tableBorderWidth+tdBorderWidth+tdPadding+tdWidth <= tableWidth）

> ps:深灰色背景的列为定义了宽度的列
> 定义宽度的列的宽度为自身定义的宽度，其他没有定义宽度的列的宽度为表格总宽度减去定义的宽度之和再平均分配

<div class="table-wrap">
<table class="tb tb-demo4 tb-fixed">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1row1row1row1row1row1row1row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5row5row5row5row5row5row5row5</td>
<td class="col6">row6</td>
<td class="col7">row7row7row7row7row7row7row7row7</td>
<td class="col8">row8row8row8row8row8row8row8row8</td>
<td class="col9">row9row9row9row9row9row9row9row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

#### 5、部分th定了宽度，同时定了th宽度的列的宽度之后大于表格宽度（tableBorderWidth+tdBorderWidth+tdPadding+tdWidth > tableWidth）
> ps:深灰色背景的列为定义了宽度的列

> 定义宽度的列的实际宽度为自身定义的宽度，其他没有定义宽度的列的宽度为表格总宽度减去定义的宽度之和再平均分配，平均分配后的宽度小于零，则其它没有定义宽度的列的宽度为0

<div class="table-wrap">
<table class="tb tb-demo5 tb-fixed">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1row1row1row1row1row1row1row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5row5row5row5row5row5row5row5</td>
<td class="col6">row6</td>
<td class="col7">row7row7row7row7row7row7row7row7</td>
<td class="col8">row8row8row8row8row8row8row8row8</td>
<td class="col9">row9row9row9row9row9row9row9row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

### 二、自动表格布局，表格设置table-layout:auto（该属性默认值就是auto）

> 每个列的宽度由单元格中没有折行的最宽的内容设定的，此种算法有时候会很慢，这是由于它需要在确定最终的布局之前访问表格中所有的列

#### 1、所有th都未定最小宽度

> 每一列的宽度完全由里面的内容所决定

<div class="table-wrap">
<table class="tb tb-demo6">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1row1row1row1row1row1row1row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5row5row5row5row5row5row5row5</td>
<td class="col6">row6</td>
<td class="col7">row7row7row7row7row7row7row7row7</td>
<td class="col8">row8row8row8row8row8row8row8row8</td>
<td class="col9">row9row9row9row9row9row9row9row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

#### 2、所有th都定义了最小宽度，根据内容计算的所有列之和小于表格宽度

> 每列宽度首先根据内容计算，同时不能小于定义的最小宽度，多余的宽度每一列上面平均分配点。

<div class="table-wrap">
<table class="tb tb-demo7">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4</td>
<td class="col5">row5</td>
<td class="col6">row6</td>
<td class="col7">row7</td>
<td class="col8">row8</td>
<td class="col9">row9</td>
<td class="col10">row10</td>
</tr>
</tbody>
</table>
</div>

#### 3、所有th都定义了最小宽度，根据内容计算的所有列之和大于表格宽度

> 每列宽度首先根据内容计算，其次不能小于定义的最小宽度

<div class="table-wrap">
<table class="tb tb-demo8">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5</td>
<td class="col6">row6row6row6row6row6row6row6row6</td>
<td class="col7">row7</td>
<td class="col8">row6row6row6row6row6row6row6row6</td>
<td class="col9">row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>

#### 4、部分th定义了最小宽度，根据内容计算的所有列之和小于表格宽度

> ps:深灰色背景的列为定义了最小宽度的列

> 每列宽度首先根据内容计算，其次不能小于定义的最小宽度，最后表格渲染出来的宽度不能小于表格自身定义的宽度。

<div class="table-wrap">
<table class="tb tb-demo9">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1</td>
<td class="col2">row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5</td>
<td class="col6">row6</td>
<td class="col7">row7</td>
<td class="col8">row6</td>
<td class="col9">row9</td>
<td class="col10">row10</td>
</tr>
</tbody>
</table>
</div>

#### 5、部分th定义了最小宽度，根据内容计算的所有列之和小于表格宽度

> ps:深灰色背景的列为定义了最小宽度的列

> 每列宽度首先根据内容计算，其次不能小于定义的最小宽度

<div class="table-wrap">
<table class="tb tb-demo10">
<thead>
<tr><th class="col1">th1</th><th class="col2">th2</th><th class="col3">th3</th><th class="col4">th4</th><th class="col5">th5</th><th class="col6">th6</th><th class="col7">th7</th><th class="col8">th8</th><th class="col9">th9</th><th class="col10">th10</th></tr>
</thead>
<tbody>
<tr>
<td class="col1">row1</td>
<td class="col2">row2row2row2row2row2row2row2row2</td>
<td class="col3">row3</td>
<td class="col4">row4row4row4row4row4row4row4row4</td>
<td class="col5">row5</td>
<td class="col6">row6row6row6row6row6row6row6row6</td>
<td class="col7">row7</td>
<td class="col8">row6row6row6row6row6row6row6row6</td>
<td class="col9">row9</td>
<td class="col10">row10row10row10row10row10row10row10</td>
</tr>
</tbody>
</table>
</div>