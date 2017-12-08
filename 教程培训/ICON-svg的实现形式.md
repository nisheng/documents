# ICON

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9eyxaehkj30zc0fe45l.jpg)

首先我们来说一下前端 icon 的发展史。

**远古时代**
在我刚开始实习时，大部分图标都是用 img 来实现的。渐渐发现一个页面的请求资源中图片 img 占了大部分，所以为了优化有了`image sprite` 就是所谓的雪碧图，就是将多个图片合成一个图片，然后利用 css 的 background-position 定位显示不同的 icon 图标。但这个也有一个很大的痛点，维护困难。每新增一个图标，都需要改动原始图片，还可能不小心出错影响到前面定位好的图片，而且一修改雪碧图，图片缓存就失效了，久而久之你不知道该怎么维护了。

**font 库**
后来渐渐地一个项目里几乎不会使用任何本地的图片了，而使用一些 font 库来实现页面图标。常见的如 [Font Awesome](https://link.juejin.im/?target=http%3A%2F%2Ffontawesome.io%2F) ，使用起来也非常的方便，但它有一个致命的缺点就是找起来真的很不方便，每次找一个图标特别的费眼睛，还有就是它的定制性也非常的不友善，它的图标库一共有675个图标，说少也不少，但还是会常常出现找不到你所需要图标的情况。当然对于没有啥特别 ui 追求的初创公司来说还是能忍一忍的。但随着公司的壮大，来了越来越多对前端指手画脚的人，丧心病狂的设计师，他们会说不！这icon这么丑，这简直是在侮辱他们高级设计师的称号啊！不过好在这时候有了[iconfont](https://link.juejin.im/?target=http%3A%2F%2Ficonfont.cn%2F) 。

**iconfont**
一个阿里爸爸做的开源图库，他的图标数量还是很惊人的，不仅有几百个公司的开源图标库，还有各式各样的小图标，还支持自定义创建图标库，所以不管你是一家创业公司还是对设计很有要求的公司，它都能很好的帮助你解决管理图标的痛点。你想要的基本都有~

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9eyhmg1hj303k03k0sj.jpg)

## iconfont 三种使用

### unicode

最开始我们使用了`unicode`的格式，它主要的特点是
**优势**

- 兼容性最好，支持ie6+
- 支持按字体的方式去动态调整图标大小，颜色等等

**劣势**

- 不支持多色图标
- 在不同的设备浏览器字体的渲染会略有差别，在不同的浏览器或系统中对文字的渲染不同，其显示的位置和大小可能会受到font-size、line-height、word-spacing等CSS属性的影响，而且这种影响调整起来较为困难

**使用方法：**
第一步：引入自定义字体 `font-face

```
 @font-face {
   font-family: "iconfont";
   src: url('iconfont.eot'); /* IE9*/
   src: url('iconfont.eot#iefix') format('embedded-opentype'), /* IE6-IE8 */
   url('iconfont.woff') format('woff'), /* chrome, firefox */
   url('iconfont.ttf') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
   url('iconfont.svg#iconfont') format('svg'); /* iOS 4.1- */
 }
```

第二步：定义使用iconfont的样式

```
.iconfont {
  font-family:"iconfont" !important;
  font-size:16px;
  font-style:normal;
  -webkit-font-smoothing: antialiased;
  -webkit-text-stroke-width: 0.2px;
  -moz-osx-font-smoothing: grayscale;
}
```

第三步：挑选相应图标并获取字体编码，应用于页面

```
<i class="iconfont">&#xe604;</i>
```

效果图：

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9ezkqbx7j310w080q52.jpg)

其实它的原理也很简单，就是通过 `@font-face` 引入自定义字体(其实就是一个字体库)，它里面规定了`&#xe604` 这个对应的形状就长这企鹅样。不过它的缺点也显而易见，`unicode`的书写不直观，语意不明确。光看`&#xe604;`这个`unicode`你完全不知道它代表的是什么意思。这时候就有了 `font-class`。

### font-class

与unicode使用方式相比，具有如下特点：

- 兼容性良好，支持ie8+
- 相比于unicode语意明确，书写更直观。可以很容易分辨这个icon是什么。

**使用方法：**
第一步：拷贝项目下面生成的fontclass代码：

```
../font_8d5l8fzk5b87iudi.css
```

第二步：挑选相应图标并获取类名，应用于页面：

```
<i class="iconfont icon-xxx"></i>
```

效果图：

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9eztfkynj310s074wgu.jpg)

它的主要原理其实是和 `unicode` 一样的，它只是多做了一步，将原先`&#xe604`这种写法换成了`.icon-QQ`，它在每个 class 的 before 属性中写了`unicode`,省去了人为写的麻烦。如 `.icon-QQ:before { content: "\e604"; }`

相对于`unicode` 它的修改更加的方便与直观。

### symbol

随着万恶的某某浏览器逐渐淡出历史舞台，svg-icon 使用形式慢慢成为主流和推荐的方法。相关文章可以参考张鑫旭大大的文章[未来必热：SVG Sprite技术介绍](https://link.juejin.im/?target=http%3A%2F%2Fwww.zhangxinxu.com%2Fwordpress%2F2014%2F07%2Fintroduce-svg-sprite-technology%2F%3Fspm%3Da313x.7781069.1998910419.50)

- 支持多色图标了，不再受单色限制。
- 支持像字体那样通过font-size,color来调整样式。
- 支持 ie9+
- 可利用CSS实现动画。
- 减少HTTP请求。
- 矢量，缩放不失真
- 可以很精细的控制SVG图标的每一部分

###svg兼容

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f0a03t0j31sw10w10u.jpg)

**使用方法：**
第一步：拷贝项目下面生成的symbol代码：

```
引入  ./iconfont.js
```

第二步：加入通用css代码（引入一次就行）：

```
<style type="text/css">
    .icon {
       width: 1em; height: 1em;
       vertical-align: -0.15em;
       fill: currentColor;
       overflow: hidden;
    }
</style>
```

第三步：挑选相应图标并获取类名，应用于页面：

```
<svg class="icon" aria-hidden="true">
    <use xlink:href="#icon-xxx"></use>
</svg>
```

使用svg-icon的好处是我再也不用发送`woff|eot|ttf|` 这些很多个字体库请求了，我所有的svg都可以内联在html内。

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f0ut80wj31160m21kx.jpg)

未来必热：SVG Sprite技术介绍

### 使用 svg-sprite

```javascript
<template>
  <svg class="svg-icon" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>

<script>
export default {
  name: 'icon-svg',
  props: {
    iconClass: {
      type: String,
      required: true
    }
  },
  computed: {
    iconName() {
      return `#icon-${this.iconClass}`
    }
  }
}
</script>

<style>
.svg-icon {
  width: 5rem;
  height: 5rem;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```



接下来我们就要自己来制作 `svg-sprite` 了。这里要使用到 [svg-sprite-loader](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fkisenka%2Fsvg-sprite-loader) 这个神器了， 它是一个 webpack loader ，可以将多个 svg 打包成 `svg-sprite` 。

我们来介绍如何在 `vue-cli` 的基础上进行改造，加入 `svg-sprite-loader`。

我们发现`vue-cli`默认情况下会使用 `url-loader` 对svg进行处理，会将它放在`/img` 目录下，所以这时候我们引入`svg-sprite-loader` 会引发一些冲突。

```
//默认`vue-cli` 对svg做的处理，正则匹配后缀名为.svg的文件，匹配成功之后使用 url-loader 进行处理。
 {
    test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
    loader: 'url-loader',
    options: {
      limit: 10000,
      name: utils.assetsPath('img/[name].[hash:7].[ext]')
    }
}
```

解决方案有两种，最简单的就是你可以将 test 的 svg 去掉，这样就不会对svg做处理了，当然这样做是很不友善的。

- 你不能保证你所有的 svg 都是用来当做 icon的，有些真的可能只是用来当做图片资源的。
- 不能确保你使用的一些第三方类库会使用到 svg。

所以最安全合理的做法是使用 webpack 的 [exclude](https://link.juejin.im/?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fmodule%2F%23rule-exclude) 和 [include](https://link.juejin.im/?target=https%3A%2F%2Fwebpack.js.org%2Fconfiguration%2Fmodule%2F%23rule-include) ，让`svg-sprite-loader`只处理你指定文件夹下面的 svg，`url-loaer`只处理除此文件夹之外的所以 svg，这样就完美解决了之前冲突的问题。
代码如下

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f140llhj30r40h2wmg.jpg)

这样配置好了，只要引入svg之后填写类名就可以了

```
import '@/src/icons/qq.svg; //引入图标

<svg><use xlink:href="#qq" /></svg>  //使用图标
```

单这样还是非常的不优雅，如果我项目中有一百个 icon，难不成我要手动一个个引入么！ **偷懒是程序员的第一生产力！！！**

## 自动导入

首先我们创建一个专门放置图标 icon 的文件夹如：`@/src/icons`，将所有 icon 放在这个文件夹下。
之后我们就要使用到 webpack 的 [require.context](https://link.juejin.im/?target=https%3A%2F%2Fwebpack.js.org%2Fguides%2Fdependency-management%2F%23require-context)。很多人对于 `require.context`可能比较陌生，直白的解释就是

> require.context("./test", false, /.test.js$/);
> 这行代码就会去 test 文件夹（不包含子目录）下面的找所有文件名以 `.test.js` 结尾的文件能被 require 的文件。
> 更直白的说就是 我们可以通过正则匹配引入相应的文件模块。

require.context有三个参数：

- directory：说明需要检索的目录
- useSubdirectories：是否检索子目录
- regExp: 匹配文件的正则表达式

了解这些之后，我们就可以这样写来自动引入 `@/src/icons` 下面所有的图标了

```
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```

之后我们增删改图标直接直接文件夹下对应的图标就好了，什么都不用管，就会自动生成 `svg symbol`了。

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f1du3m3j31ti088ajc.jpg)

## 更进一步优化自己的svg

首先我们来看一下 从 `阿里iconfont` 网站上导出的 svg 长什么样？

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f2gpm3kj31z20h0u0x.jpg)

没错虽然 iconfont 网站导出的 svg 内容已经算蛮精简的了，但你会发现其实还是与很多无用的信息，造成了不必要的冗余。就连 iconfont 网站导出的 svg 都这样，更不用说那些更在意 ui漂不漂亮不懂技术的设计师了(可能)导出的svg了。好在 `svg-sprite-loader`也考虑到了这点，它目前只会获取 svg 中 path 的内容，而其它的信息一概不会获取。生成 svg 如下图：

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f2q1wtsj31te06odle.jpg)

但任何你在 path 中产生的冗余信息它就不会做处理了。如注释什么的

![](http://ww1.sinaimg.cn/large/005QDhBjgy1fm9f2y6e07j310606uako.jpg)

这时候我们就要使用另一个很好用的东西了-- [svgo](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fsvg%2Fsvgo)

> SVG files, especially exported from various editors, usually contain a lot of redundant and useless information such as editor metadata, comments, hidden elements, default or non-optimal values and other stuff that can be safely removed or converted without affecting SVG rendering result.

它支持几十种优化项，非常的强大，8k+的star 也足以说明了问题。。

