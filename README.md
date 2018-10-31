* #### [Vue相关](#vue相关-1)
  1. [使用sass](#1使用sass)
  2. [vue数据便利后进行初始化](#2vue数据便利后进行初始化)
  3. [vue中常用图片上传](#3vue中常用图片上传)
  4. [vue中进行跨域代理及发布注意事项](#4vue中进行跨域代理及发布注意事项)
  5. [使用material-icons](#5使用material-icons)
  6. [监听滚动条到底部](#6监听滚动条到底部)
  7. [关于Vue打包之后文件路径出错的问题](#7关于vue打包之后文件路径出错的问题)
  8. [使用jquery](#8使用jquery)
  9. [删除路由中'#'号](#删除路由中'#'号)

## Vue相关
### 1、使用sass
来源： https://www.cnblogs.com/crazycode2/p/6535105.html

>cnpm install --save-dev sass-loader style-loader css-loader

>cnpm install node-sass --save-dev

打开webpack.base.config.js在loaders里面加上  module -- rules
```
  rules: [
    ......
    {
      test: /\.scss$/,
      loaders: ["style", "css", "sass"]
    }
  ]
```

然后使用scss
```
<style lang="scss" src="@/styles/main.scss"></style>
```

### 2、vue数据便利后进行初始化
```
that.$axios.get(url).then(function (res) {
  that.rollingNew = res.data.content;
  //重点。在$nextTick方法内进行初始化
  that.$nextTick(() => {  // 下一个UI帧再初始化swiper
    that.initSwiper();
  });
  console.log(that.rollingNew)
})
```

### 3、vue中常用图片上传。
```
changeImg(e) {
  let token = localStorage.getItem('token')
  let _this = this
  let imgLimit = 1024
  let files = e.target.files
  let image = new Image()
  if (files.length > 0) {
    let dd = 0
    let timer = setInterval(function () {
      if (files.item(dd).type !== 'image/png' && files.item(dd).type !== 'image/jpeg' && files.item(dd).type !== 'image/gif') {
        return false
      }
      if (files.item(dd).size > imgLimit * 102400) {
      } else {
        image.src = window.URL.createObjectURL(files.item(dd))
        image.onload = function () {
          // 默认按比例压缩
          let w = image.width
          let h = image.height
          let scale = w / h
          w = 200
          h = w / scale
          // 默认图片质量为0.7，quality值越小，所绘制出的图像越模糊
          let quality = 0.7
          let canvas = document.createElement('canvas')
          let ctx = canvas.getContext('2d')
          // 创建属性节点
          let anw = document.createAttribute('width')
          anw.nodeValue = w
          let anh = document.createAttribute('height')
          anh.nodeValue = h
          canvas.setAttributeNode(anw)
          canvas.setAttributeNode(anh)
          ctx.drawImage(image, 0, 0, w, h)
          let ext = image.src.substring(image.src.lastIndexOf('.') + 1).toLowerCase()
          let base64 = canvas.toDataURL('image/' + ext, quality)
          if (_this.user.profileUrl !== null || _this.user.profileUrl !== undefined || _this.user.profileUrl !== '') {
            _this.user.profileUrl = base64
            //以上部分进行图片转换为base64

            //下面进行base64上传
            .....
            .....
            .....
          }
        }
      }
      if (dd < files.length - 1) {
        dd++
      } else {
        clearInterval(timer)
      }
    }, 1000)
  }
}
```
### 4、vue中进行跨域代理及发布注意事项
#### 1.在config文件中的index.js里，不管dev(开发环境下)与build(发布环境下)都应加入以下代码：
```
proxyTable: {
  '/api': {
    target: 'http://23.101.9.18:8090/apelink',
    secure: false,
    changeOrigin: true,
  	pathRewrite: {
      '^/api': '/'
    }
  }
},
```
#### 2.在build文件夹中的webpack.prod.conf.js里，含义以下代码:
```
new OptimizeCSSPlugin({
  cssProcessorOptions: config.build.productionSourceMap
  ? { safe: true, map: { inline: false } }
  : { safe: true }
}),
```
#### 此段代码需要注释掉，目前发现其对于-webkit-box-orient: vertical;会进行消除，
#### 注意：注释掉此代码后，webpack不会对css进行压缩，所以要在utils.js文件里下面代码中加入minimize: true，如下：
```
const cssLoader = {
  loader: 'css-loader',
  options: {
      sourceMap: options.sourceMap,
      minimize: true
  }
}
```

### 5、使用material-icons
> $ npm install material-design-icons

main.js全局引入
```
import 'material-design-icons/iconfont/material-icons.css'
```

### 6、监听滚动条到底部
```
created(){
  window.onscroll = function(){
    //变量scrollTop是滚动条滚动时，距离顶部的距离
    var scrollTop = document.documentElement.scrollTop||document.body.scrollTop;
    //变量windowHeight是可视区的高度
    var windowHeight = document.documentElement.clientHeight || document.body.clientHeight;
    //变量scrollHeight是滚动条的总高度
    var scrollHeight = document.documentElement.scrollHeight||document.body.scrollHeight;
    //滚动条到底部的条件
    if(scrollTop+windowHeight==scrollHeight){
      //写后台加载数据的函数
      console.log("距顶部"+scrollTop+"可视区高度"+windowHeight+"滚动条总高度"+scrollHeight);
    }   
  }
}
```

或者是用div内滚动到90%
```
<div v-on:scroll.passive="onScroll" ref="page">
  ......
</div>

data () {
  return {
    finish: true
  }
}

onScroll () {
  var scrollTop = this.$refs.page.scrollTop;
  var windowHeight = this.$refs.page.clientHeight;
  var scrollHeight = this.$refs.page.scrollHeight;
  if((scrollTop / (scrollHeight - windowHeight) >= 0.9) && this.finish){
    this.finish = false
    this.$axios.post(url).then(res = > {
      ...
      this.finish = true
    })
  }   
}
```

### 7、关于Vue打包之后文件路径出错的问题
#### 1.文件路径不对
找到config文件夹下的index.js文件修改一下位置;

![文件路径不对](https://raw.githubusercontent.com/ZHAO-Shaofeng/Related-considerations-for-HTML-CSS-JS/master/github-img/vue1.png)

 build（上边还有个dev 是开发环境下的配置，不需要改动）下的 assetsPublicPath ：将'/'改为'./'

#### 2.背景图片路径不对

css中写的background-img的路径出错 需要找到build文件夹下的utils.js,修改一下位置

![背景图片路径不对](https://raw.githubusercontent.com/ZHAO-Shaofeng/Related-considerations-for-HTML-CSS-JS/master/github-img/vue2.png)

加入红框内字段即可

### 8、使用jquery

>cnpm install jquery --save

在项目根目录下的build目录下找到**webpack.base.conf.js**文件，在开头使用以下代码引入webpack，因为该文件默认没有引用

```
var webpack = require('webpack')
```

**然后在module.exports中添加一段代码**

```
// 原有代码
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
'vue$': 'vue/dist/vue.esm.js',
'@': resolve('src')
  }
},
// 添加代码+++++++++++++++
plugins: [
  new webpack.ProvidePlugin({
    $: "jquery",
    jQuery: "jquery",
    jquery: "jquery",
    "window.jQuery": "jquery"
  })
],
+++++++++++++++++++++++++
// 原有代码
module: {
  rules: [
// ......
  ]
}
```

最后在main.js里导入，然后重跑一下项目

```
import 'jquery'
```

如果有 eslint 检查报错，改文件的module.exports中，为env添加一个键值对 jquery: true 就可以了

```
env: {
  // 原有
  browser: true,
  // 添加
  jquery: true
}
```
### 9、删除路由中'#'号
```
const router = new VueRouter({
  mode: 'history', //在路由中加入此属性
  routes: [...]
})
```
### 

#### npm使用淘宝镜像
>npm install -g cnpm --registry=https://registry.npm.taobao.org
