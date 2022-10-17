---
layout: post
title: 'vue表格组件封装'
date: '2022-10-17'
tags: 'vue element table'
author: 'guozhaodong'
---

# vue表格组件封装

### 问题

 项目中的表格都是用的`element-ui`中的`table`组件，现在项目需要做移动端适配，表格在移动端展示需要是卡片形式的，pc端还是`table`形式的，如下图：

 ![image](/assets/img/vue-table/20221017153348.png)

图一：pc端


 ![image](/assets/img/vue-table/20221017153514.jpg)

图二：移动端

由于需要`pc`上渲染出来的`html`是
`<table><tr><td></td></tr></table>`形式的，而移动端上渲染出来是`<div class="card"><div class="card-label"></div><div class="card-cont"></div></div>`这种形式的，但是下面是`element` `table`组件的代码，表格的每一栏都是一个`el-table-column`模板代码，结构是固定的，所以在移动端上没办法复用这个`template`，于是我们需要另辟蹊径


``` vue
<template>
    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="date"
        label="日期"
        width="180">
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="180">
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址">
      </el-table-column>
    </el-table>
  </template>

  <script>
    export default {
      data() {
        return {
          tableData: [{
            date: '2016-05-02',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1518 弄'
          }, {
            date: '2016-05-04',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1517 弄'
          }, {
            date: '2016-05-01',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1519 弄'
          }, {
            date: '2016-05-03',
            name: '王小虎',
            address: '上海市普陀区金沙江路 1516 弄'
          }]
        }
      }
    }
  </script>
```


### 方案一

通过slot传参，再搞一个移动端的`template`，代码如下


``` vue
<kl-table :data="tableData" :columns="earnRecordColumns" :loading="!isIntervalReload && fetchDataLoading && hasShow" :handleCurrentChange="handleCurrentChange" :handleSizeChange="handleSizeChange" :pagesize="pagesize" :currentPage="currentPage" :pageSizes="pageSize" :totalNum="totalNum">
      <template v-slot:profit>
        <el-table-column prop="profit" label="收益（$）">
          <template slot-scope="scope">{{ scope.row.profit | formatCurrency }}</template>
        </el-table-column>
      </template>
      <template v-slot:mobileProfit="scope">
        <table-card-column label="收益（$）">
          <template>{{ scope.row.profit | formatCurrency }}</template>
        </table-card-column>
      </template>

      <template v-slot:profitOfRate>
        <el-table-column prop="profit" label="收益率">
          <template slot-scope="scope">
            <div class="profit-style">{{ scope.row.profitOfRate }}</div>
          </template>
        </el-table-column>
      </template>
      <template v-slot:mobileProfitOfRate="scope">
        <table-card-column label="收益率">
          <template>
            <div class="profit-style">{{ scope.row.profitOfRate }}</div>
          </template>
        </table-card-column>
      </template>
</kl-table>


this.earnRecordColumns = [
    {
        slot: 'profit',
        mobileSlot: 'mobileProfit',
    },
    {
        slot: 'profitOfRate',
        mobileSlot: 'mobileProfitOfRate',
    },
```

上面代码中`v-slot:profit`是pc端的`template`,`v-slot:mobileProfit`是移动端的`template`这样就能达到目的，但是这种方式太鸡肋啦，没弄一个字段，如果需要自定义`template`则需要写一遍`pc`的，还要写一遍`mobile`，作为一个有追求的搬砖工绝对不允许这种重复工作（没时间摸鱼了..）。


### 方案二

> 思考一下：如果我们能把字段信息和`template`分离出来，也就是不用`template`去设置自定义渲染的字段，我们换成通过`jsx`的形式把渲染模板结构抽离出来，自定义的模板通过一处`jsx`传递给`pc端`和`移动端`的模板，这样就可以只写一处`jsx`代码了


``` vue
<kl-table
    :data="positionHistoryData"
    :columns="columns"
    :loading="!isPositionHistoryIntervalReload && fetchPositionHistoryLoading"
    :handleCurrentChange="handleCurrentChange"
    :handleSizeChange="handleSizeChange"
    :pagesize="pagesize"
    :currentPage="currentPage"
    :pageSizes="pageSize"
    :totalNum="totalNum"
    >
</kl-table>



this.columns = [
    {
        prop: 'profit',
        label: '收益',
        'min-width': '120',
        render: (scope) => {
            return formateToUsd(scope.row.profit)
        },
    },
    {
        prop: 'profitOfRate',
        label: '收益率',
        width: '120',
        render: (scope) => {
            return  <div class="profit-style">{scope.row.profitOfRate}</div>
        },
    },
]
```

如果字段比较多，那么就会省不少代码


#### 最后看下组件代码，代码中包含了方案一和方案二（一开始使用的方案一，后来迁移到方案二）

#### `klTable.vue`

``` vue
<template>
  <table-card :data="data" :loading="loading">
    <!-- pc表格 -->
    <el-table :data="data" :key="tableKey" :max-height="maxHeight" style="width: 100%" v-loading="loading">
      <template v-for="(column, index) in columns">
        <template v-if="!('v-if' in column) || column['v-if']">
          <slot v-if="column.slot" :name="column.slot"></slot>
          <!-- 如果有render，则为jsx传入 -->
          <el-table-column v-else-if="column.render" v-bind="column" :key="index">
            <template slot-scope="scope">
              <Jsx :jsx="column.render" :scope="scope"></Jsx>
            </template>
          </el-table-column>
          <el-table-column v-else v-bind="column" :key="index"> </el-table-column>
        </template>
      </template>
      <slot></slot>
    </el-table>

    <!-- pc分页 -->
    <el-pagination v-if="pagination" @size-change="handleSizeChange" @current-change="handleCurrentChange" :page-size="pagesize" :current-page="currentPage" :page-sizes="pageSizes" layout="total, sizes, prev, pager, next, jumper" :total="totalNum" style="margin-top: 25px; text-align: right">
    </el-pagination>

    <!-- mobile卡片式表格 -->
    <template v-slot:mobile="scope">
      <template v-for="(column, index) in columns">
        <template v-if="!('v-if' in column) || column['v-if']">
          <slot v-if="column.mobileSlot" :name="column.mobileSlot" :row="scope.row"></slot>
          <table-card-header v-else-if="column.isHeader" :key="index">
            <!-- 如果有render，则为jsx传入 -->
            <template v-if="column.render">
              <Jsx :jsx="column.render" :scope="{ ...scope }"></Jsx>
            </template>
            <template v-else>
              {{ scope.row[column.prop] }}
            </template>
          </table-card-header>

          <table-card-column v-else :label="column.label" :key="index">
            <!-- 如果有render，则为jsx传入 -->
            <template v-if="column.render">
              <Jsx :jsx="column.render" :scope="{ ...scope, $index: scope.index }"></Jsx>
            </template>
            <template v-else>
              {{ scope.row[column.prop] }}
            </template>
          </table-card-column>
        </template>
      </template>
      <slot name="mobile" :row="scope.row"></slot>
    </template>

    <!-- mobile分页 -->
    <template v-slot:mobilePagination v-if="pagination">
      <el-pagination @current-change="handleCurrentChange" small :pager-count="5" :page-size="pagesize" :current-page="currentPage" :page-sizes="pageSizes" layout="total, prev, pager, next" :total="totalNum" style="margin-top: 25px; text-align: right"> </el-pagination>
      <!-- <div v-if="loading" class="loading-wrap"><i class="el-icon-loading"></i></div>
      <table-card-more v-else :load-more="loadMore" v-show="currentPage != Math.ceil(totalNum / pagesize)"></table-card-more> -->
    </template>
  </table-card>
</template>

<script>
import Jsx from './jsx.vue'
export default {
  name: 'KlTable',
  props: {
    loading: Boolean,
    data: Array,
    columns: Array,
    handleSizeChange: Function,
    handleCurrentChange: Function,
    pagesize: Number,
    currentPage: Number,
    pageSizes: Array,
    totalNum: Number,
    tableKey: String,
    maxHeight: [String, Number],
    pagination: {
      type: Boolean,
      default: true,
    },
    // loadMore: Function,
  },
  components: { Jsx },
  created() {},
}
</script>
<style lang="scss" scoped>
/deep/ .el-table__fixed,
/deep/ .el-table__fixed-right {
  height: 100% !important; //设置高优先，以覆盖内联样式
}
.loading-wrap {
  display: flex;
  justify-content: center;
}
</style>

```

#### `jsx.vue`

``` vue
<script>
export default {
  props: {
    jsx: {
      type: Function,
    },
    scope: Object,
  },
  render() {
    return <div>{this.jsx(this.scope)}</div>
  },
  name: 'jsx',
}
</script>
```


#### `tableCard.vue`

``` vue
<template>
  <div class="table-container">
    <div class="table-wrap">
      <slot></slot>
    </div>
    <div class="table-mobile-wrap">
      <template v-if="loading || (data && data.length > 0)">
        <div class="tb-card-wrap" v-loading="loading">
          <div class="tb-card" v-for="(item, index) in data" :key="index">
            <slot name="mobile" :row="item" :index="index"></slot>
          </div>
        </div>
        <slot name="mobilePagination" v-if="data && data.length > 0"></slot>
      </template>

      <div class="no-data" v-else>
        <el-empty :image-size="200"></el-empty>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TableCard',
  props: {
    data: Array,
    loading: Boolean,
  },
  created() {
    // console.log('table $props', this)
  },
}
</script>
<style lang="scss" scoped>
.tb-card {
  margin-bottom: 20px;
  padding: 32px 24px;
  background: #fff;
}
.no-data {
  background: #fff;
}
.tb-card-wrap {
  min-height: 300px;
}
.table-wrap {
  display: block;
}
.table-mobile-wrap {
  display: none;
}
@media (max-width: 767px) {
  .table-wrap {
    display: none;
  }
  .table-mobile-wrap {
    display: block;
    background-color: #ecf0f5;
    margin: -16px -24px;
  }
}
.el-dialog__wrapper {
  .table-mobile-wrap {
    background-color: transparent;
    margin: 0;
    .tb-card {
      background: rgba(151, 160, 168, 0.05);
      border-left: 2px solid #97a0a8;
    }
  }
}
</style>


```

#### `tableCardColumn.vue`

``` vue
<template>
  <div>
    <div class="tb-lst-item">
      <span class="tb-card-label">{{ label }}</span>
      <span class="tb-card-cont">
        <slot></slot>
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TableCardColumn',
  props: {
    label: String,
  },
  created() {
    // console.log('table $props', this)
  },
}
</script>
<style lang="scss" scoped>
.tb-lst-item {
  align-items: center;
  margin-top: 16px;
  display: flex;
  justify-content: space-between;
  font-size: 14px;
  line-height: 20px;
}
.tb-card-label {
  color: rgba(0, 0, 0, 0.65);
}
.tb-card-cont {
  color: rgba(0, 0, 0, 0.85);
  font-weight: 600;
  text-align: right;
}
</style>

```

#### `tableCardHeader.vue`

``` vue
<template>
  <div class="tb-card-hd">
    <h3 class="tb-card-tit"><slot name="default"></slot></h3>
    <span class="tb-card-sub"><slot name="subTitle"></slot></span>
  </div>
</template>

<script>
export default {
  name: 'TableCardHeader',
  props: {
    label: String,
  },
  created() {
    // console.log('table $props', this)
  },
}
</script>
<style lang="scss" scoped>
.tb-card-hd {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #bccae0;
  padding-bottom: 16px;
  line-height: 24px;
  color: rgba(0, 0, 0, 0.85);
}
.tb-card-tit {
  font-weight: 600;
  font-size: 16px;
}
.tb-card-sub {
  font-weight: 400;
  font-size: 12px;
}
</style>

```