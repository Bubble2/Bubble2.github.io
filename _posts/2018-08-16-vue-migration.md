---
layout:     post
title:      "vue1.0升级2.0踩坑"
date:       "2018-08-16"
tags:        "vue"
author:     "guozhaodong"
---

# vue升级2.0踩坑

之前一直在搞`react`，最近项目组要对一个移动端的项目说要增加新功能，但是这个项目好久没有维护和跟新了，最关键还是用的`vue1.0`开发的，这让我有点慌。

有两个方法，一是在目前`1.0`版本的基础上开发，还有一个就是把项目直接升级到`2.0`。

后来经过我综合考虑决定试着去升级，原因如下：

1. 1.0的相关文档不怎么好找，也不全（包括`vue`,`vuex`,`vue-router`，还有其它用到的一些插件）

2. 如果不升级新的东西不能用，而且后续如果再升级，难度会跟大些

3. 通过升级过程，自己也去更加熟悉下`vue`以及项目中的代码和业务等

看了下官网有提供迁移的文档，[文档点这里](https://cn.vuejs.org/v2/guide/migration.html)，里面提供了`vue`、`vuex`、`vue-router`升级之后变化的`api`，同时还提供了一个命令行工具`vue-migration-helper`，可以用于检测你的项目中有哪些`api`是需要去升级的。

## 升级`package.json`中的依赖包

`vue`  `^1.0.21` => `^2.5.16`

`vue-router` `^0.7.13` => `^3.0.1`

`vuex` `^0.6.3` => `^3.0.1`

`vue-touch` `^1.1.0` => `^2.0.0-beta.4`

`vue-router-sync` `^1.0.0` => `^5.0.0`

`vue-validator`官网不再维护了直接废弃，换成了`vuelidate ^0.7.4` [点击查看api](https://monterail.github.io/vuelidate/)
之前的`currency`过滤器不能用了，新增了一个`accounting ^0.4.1`，用于处理金额货币格式化

## 用`vue-migration-helper`逐个目录去检测

检测会提醒你哪个地方需要修改，怎么修改，如果还不清楚，可以去这个升级文档里面去看详细的修改方法。

## 下面介绍下升级过程中遇到的一些难点以及一些解决方法

### 1、 `vuex`官网升级文档只是从`0.6`升级到`1.0`，那么我是直接升级到`3.0`的，需要做哪些修改呢？

(1)、从`vuex`中获取`state`和`actions`的方式变了，之前是通过构造函数的`vuex`选项参数来获取的，[api参考链接](https://github.com/vuejs/vuex/blob/1.0/docs/zh-cn/state.md)，现在是通过`mapState`和`mapActions`这两个辅助函数来获取的，然后直接放在`computed`和`methods`上面，相当于把它们都变成了`vue`的实例属性。

先看下老代码
``` javascript
    import dataPicker from  './data_pick.vue'
    import {getHome} from '../../vuex/actions/home'
    import {hideNoData, hideLoading} from '../../vuex/actions/app'
    export default {
        component: {dataPick},
        data(){
            return {
                showPersonal: false,
                currType: '0',
                dataSelected: '4'
            }
        },
        vuex: {
            getters: {
                home: ({homeStore}) => homeStore.home,
                showErrorMes: ({app}) => app.showErrorMes
            },
            actions: {
                getHome, hideNoData, hideLoading
            }
        }
    }
```

升级后的代码

``` javascript
    import {mapState, mapActions} from 'vuex'
    import dataPick from './data_pick.vue'
    export default {
        component: {dataPick},
        data(){
            return {
                showPersonal: false,
                currType: '0',
                dataSelected: '4'
            }
        },
        computed: {
            ...mapState({
                home: ({homeStore}) => homeStore.home,
                showErrorMes: ({app}) => app.showErrorMes
            })
        },
        methods: {
            ...mapActions([
                'goHome',
                'hideNoData',
                'hideLoading'
            ])
        }
    }
```

(2)、因为获取的方式改变了，升级之后所有的`state`、`getters`、`mutations`、`actions`都应该放在`modules`下面，统一暴露对外接口

升级前的代码

`vuex/actions/home.js`
``` javascript
    export const getHome = (store) => {
        store.dispatch(types.HIDE_NODATA);
        ...
    }
```

`vuex/modules/home.js`
``` javascript
    const state = {
        ...
    }

    const mutations = {
        ...
    }

    export default {
        state,
        mutations
    }
```

升级后的代码

删除`vuex/actions/home.js`这个文件

`vuex/modules/home.js`
```javascript
    const state = {
        ...
    }

    const mutations = {
        ...
    }

    const actions = {
        getHome(){
            ...
        }
    }

    export default {
        state,
        mutations,
        actions
    }
```

(3)、 `vuex`的`actions`里面如果要用到路由的`router`对象，现在的`actions`方法里面已经不传`store`对象了，所以是没法直接取到了。

不过我们可以通过在组件里面调用`actions`的时候把`$router`传进去。

```javascript
    const actions = {
        logOut(context, $router){
            login.logOut().then(response => {
                const data = response.data
                if(data && data.result == 'success'){
                    $router.replace({name: 'login', query: {regid: data.regid, ostype: data.ostype}});
                    location.href = location.href;
                }
            })
        }
    }
```


### 2、路由的`data`钩子方法替换问题

先看下之前的代码
``` javascript
    export default {
        route: {
            data(){
                ...//调用初始化页面方法
            }
        }
    }
```

官网上给出了`data`替换的方法，是通过`watch`去监测`$route`的变化

``` javascript
 export default {
     watch: {
         '$route': 'fetchData'
     },
     methods: {
         fetchData: function(){
             ...//调用初始化页面方法
         }
     }
 }
```
但是这种方式有一个问题就是，不管是进入这个页面还是离开这个页面，只要路由有变化，都会调用`fetchData`，这样的话，如果就会出现发送无用请求的问题，可能还会出现一些无法意料不可控的问题。
此外，如果路由变化到另外一个页面，需要获取目标路由`to`和来源路由`from`，用这个方法也不是很方便。

后来研究了`vue-router`里组件内的守卫，发现有一个方法可以满足要求，`beforeRouteEnter`这个方法，这个方法虽然不能获取组件实例`this`，但是它有一个回调函数，在里面可以获取组件实例

``` javascript
export default {
    beforeRouterEnter(to, from, next){
        next(vm => {
            vm.fetchData()
        })
    },
    methods: {
        fetchData: function(){
            ...//调用初始化页面方法
        }
    }
}
```

还有一个问题是，如果是同一个路由只是切换参数，`beforeRouterEnter`是不会触发的，这个时候可以再加一个`beforeRouterUpdate`，这个方法只会在当前路由改变，但是该组件被复用时调用。

``` javascript
export default {
    beforeRouterEnter(to, from, next){
        next(vm => {
            vm.fetchData()
        })
    },
    beforeRouterUpdate(to, from, next){
        this.fetchData()
        next();
    },
    methods: {
        fetchData: function(){
            ...//调用初始化页面方法
        }
    }
}
```

### 3、 `vue-validator`不再是官网插件了，官网也不再维护了，所以没有升级的必要了（貌似也没有升级的版本），所以找了半天找了一个`vuelidate`来替换

![image](/assets/img/vue-migration/vue-validator.jpg)

官网发布的版本已经不再兼容`vue2.0`了，果断放弃，从官网发现了一个可以找`vue`插件的网站，[https://curated.vuejs.org/](https://curated.vuejs.org/)

走这个上面找到了`vuelidate`，[api点击这里](https://monterail.github.io/vuelidate/)

### 4、关于请求没有返回之前，无数据导致对象层次比较深的代码报错

升级之后，如果从后端异步请求过来的数据层级比较深，需要做非空判断，不然页面加载完成，数据还没有返回过来时候会报错。

父组件
```html
    <template>
        <div>
            <order detail="detail"></order>
        </div>
    </template>
    <script>
        import {order} from 'app/business/order.vue'
        export default {
            component:{order},
            computed:{
                ...mapState({
                    detail: ({detailStore}) => detailStore.detail
                })
            }
        }
    </script>
```

子组件`app/business/order.vue`
```html
    <template>
        <div class="hd">{{detail.order.title}}</div>
        <div class="bd">
            <ul>
                <li>采购订单:{{detail.order.orderNo}}</li>
                <li>采购日期：{{detail.order.orderTime}}</li>
            </ul>
        </div>
    </template>
    <script>
        import {order} from 'app/business/order.vue'
        export default {
            props:['detail']
        }
    </script>
```

上面的子组件在页面渲染好之后，detail是从`vuex`中的异步请求过来的数据，当数据还没有返回时，上面的`detail`值是`''`，那么`detail.order`值就为`undefined`，那么`detail.order.title`就肯定会报错，此时你可以使用`{{detail && detail.order && detail.order.title}}`这种方式进行空值判断，但是如果页面中有很多这种值，那么就会很冗余。

此时我们可以在父组件传给子组件属性的时候用`v-if`进行判断，如果detail是个空对象就不进行渲染

```html
    <template>
        <div>
            <order detail="detail" v-if="Object.keys(detail).length > 0"></order>
        </div>
    </template>
```

### 5、 升级之后如果不引入完整版`vue`的话，默认只包含运行时版本，不包含编译器，这就会导致编译模板报错

`[Vue warn]: You are using the runtime-only build of Vue where the template option is not available. Either pre-compile the templates into render functions, or use the compiler-included build`

如果发现报上面这行警告是因为升级后当你通过`import vue from 'vue'`引入的其实是运行时的版本，是不包含编译器的，如果你使用`webpack`，此时我们可以通过
配置下让其默认引用的是完整版。

``` javascript
resolve: {
  alias: {
    vue: 'vue/dist/vue.esm.js'
  }
}
```






