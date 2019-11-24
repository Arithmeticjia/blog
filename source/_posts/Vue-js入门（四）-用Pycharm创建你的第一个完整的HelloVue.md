---
title: Vue.js入门（四）---用Pycharm创建你的第一个完整的HelloVue
date: 2019-11-23 19:41:17
tags:
---
本文主要介绍了如何在Pycharm中构建一个简单的vue应用,摘要要写50个字，不然markdown格式会很丑，我也不知道要写啥，感觉没啥好写的，现在应该差不多了。
<!-- more -->
## 一、在Pycharm中下载Vue.js插件
![](https://www.guanacossj.com//media/articlebodypics/1571819210811.jpg)
## 二、新建一个Vue.js项目
![](https://www.guanacossj.com//media/articlebodypics/1571819312914.jpg)
然后等待安装启动完成，这里提示我要不要用淘宝源，我果断同意啊
## 三、项目启动
![](https://www.guanacossj.com//media/articlebodypics/1571819476507.jpg)
##  四、项目目录结构
![](https://www.guanacossj.com//media/articlebodypics/1571819572736.jpg)
``````
'''
-node_modules：项目的依赖
-public
    -favicon.ico  网页的图标
    -index.html   主页面
-src：我们需要关注的
    -assets：方静态文件
    -components：小组件
    -views  ：页面组件
    -App.vue ：主组件
    -main.js ：项目主入口js
    -router.js： 路由相关，以后配置路由，都在这里配置
    -store.js  ：vuex相关，状态管理器
-package.json   项目的依赖文件
'''
``````
##  五、修改默认的启动页
在components下新建一个HelloVue
```html
<template>
    <div class="hello">
        <h1>{{msg}}</h1>
    </div>
</template>
 
<script>
    export default {
        name: "HelloVue",
        data () {
        return {
            msg: 'HelloVue'
            }
        }
    }
</script>
 
<style scoped>
 
</style>
```
修改App.vue
```
import HelloWorld from './components/HelloVue.vue'
```
效果图
![](https://www.guanacossj.com//media/articlebodypics/1571819861870.jpg)