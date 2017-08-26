# 关于前端雪碧图相关知识点

-----------------------------------我是华丽的分割线-------------------------------------------
    
但凡作为一个合格的前端工程师，在优化前端性能这块关于图片这块的优化，都会想到用上雪碧图；
雪碧图对很多前端来说，又爱又恨，明明知道必须要搞它，然而有时候又不是很想搞它。在我看来是效率问题吧，因为能做出雪碧图的方式有很多种，要适合不同的场景才行。
我总结了有以下集中生成sprite的方式：

1、用软件生成：   
- photoshop   
- [css雪碧图简单制作工具](https://github.com/iwangx/sprite "css雪碧图简单制作工具")  
2、用构建工具
- sass
- gulp、grunt、webpack.....

用photoshop 这种刀耕火种年代的工具自然不能作为推荐，个人觉得CssSprite 这软件特别好用，而且还能用在移动端，详细介绍可以看这篇[css sprite css雪碧图生成工具](http://developer.51cto.com/art/201504/474506.htm "css sprite css雪碧图生成工具")。
然后说说构建工具，我用的sass比较多，然后发现sass其实对sprite研究颇深。。。如果用在大项目上，自然得配合上gulp、grunt、webpack来用才好。

-----------------------------------我是华丽的分割线-------------------------------------------

其实我觉得用哪种方式还得看效率，比如有一个活动页面psd要求今天下班前上线，总不能用gulp、grunt、webpack这些大牛来写好配置再搞。而且有些前端大团队，限制很多，比如我之前的公司构建工具只能用grunt，我发现grunt某些任务真的比gulp慢的要死，这里扯远了。。。。下面说说分别说说sprite PC端和移动端的用法，主要以sass来讲，因为gulp，grunt，webpack这些工具都是有sass的相关插件

## PC端
demo里面pc-sprite.scss的配置项如下：

```
@import "compass";// 引入compass模块
@import "compass/utilities/sprites"; // 引入compass模块
$icons-spacing: 5px;  //每个图标的空隙，太挤会出现问题
$icons-layout: smart; //图标排布的方式
$icons-sprite-dimensions:true;//所有图标设置宽高
.sprite-block{display: inline-block;} //sass虽然会计算出每个图标的宽高，一般还得再加上inline-block 这属性。
$icons-sprite-base-class:'.sprite-block';//为每一个图标添加上面的样式
@import "icons/*.png";  // 在images定义一个名为“icons”的文件夹存放小图标
@include all-icons-sprites; // (为每一个图片生成代码)
.other1{@extend .icons-2;}
.other2{@include icons-sprite(2);}
//用了两种方式来引用第二个图标，从渲染的结果来看，用extend更好一点，不过最后通过gulp、grunt这些工具压缩，这个问题也就忽略吧

```
通过sass编译后的代码：
```
.sprite-block, .icons-1, .icons-2, .other1, .other2 { display: inline-block; }
.sprite-block, .icons-1, .icons-2, .other1, .other2 { background-image: url('../images/icons-s1a2b006496.png'); background-repeat: no-repeat; }
.icons-1 { background-position: 0 0; height: 42px; width: 28px; }
.icons-2, .other1 { background-position: 0 -42px; height: 42px; width: 28px; }
.other2 { background-position: 0 -42px; height: 42px; width: 28px; }
```

## 移动端
 - 只有一个配置需要根据自身项目来修改的： $base-fonts-default: 100px; //基准数，也就是对应你的media css或者js换算基准。
 
 - 详细设置原理参考[使用sass与compass合并雪碧图][1]博文


  [1]: http://www.cnblogs.com/xljzlw/p/4771103.html
