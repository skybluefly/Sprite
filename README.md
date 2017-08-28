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

用photoshop 这种刀耕火种年代的工具自然不能作为推荐，个人觉得CssSprite 这软件特别好用，而且还能用在移动端，详细介绍可以看这篇[css sprite css雪碧图生成工具](http://developer.51cto.com/art/201504/474506.htm "css sprite css雪碧图生成工具")。我一般是先考虑用sass，若不行就改为用这个软件了，sass其实对sprite研究颇深。。。如果用在大项目上，自然得配合上gulp、grunt、webpack来用才好。

-----------------------------------我是华丽的分割线-------------------------------------------

其实我觉得用哪种方式还得看效率，比如有一个活动页面psd要求今天下班前上线，总不能用gulp、grunt、webpack这些大牛来写好配置再搞。而且有些前端大团队，限制很多，比如我之前的公司构建工具只能用grunt，我发现grunt某些任务真的比gulp慢的要死，这里扯远了。。。。下面说说分别说说sprite PC端和移动端的用法，主要以sass来讲，因为gulp，grunt，webpack这些工具都是有sass的相关插件

## PC端
pc端sprite的配置项如下：

```
@import "compass";// 引入compass模块
@import "compass/utilities/sprites"; // 引入sprites模块
$icons-spacing: 5px;  //每个图标的空隙，太挤会出现问题
$icons-layout: smart; //图标排布的方式
$icons-sprite-dimensions:true;//所有图标设置宽高
.sprite-block{display: inline-block;} //sass虽然会计算出每个图标的宽高，一般还得再加上inline-block 这属性。
$icons-sprite-base-class:'.sprite-block';//为每一个图标添加上面的样式
@import "icons/*.png";  // 在images定义一个名为“icons”的文件夹存放小图标
@include all-icons-sprites; // (为每一个图片生成代码)
.other1{@extend .icons-2;}
.other2{@include icons-sprite(2);}//用了两种方式来引用第二个图标，从渲染的结果来看，用extend更好一点，不过最后通过gulp、grunt这些工具压缩，这个问题也就忽略吧

```
通过sass编译后的代码：
```
.sprite-block, .icons-1, .icons-2, .other1, .other2 { display: inline-block; }
.sprite-block, .icons-1, .icons-2, .other1, .other2 { background-image: url('../images/icons-s1a2b006496.png'); background-repeat: no-repeat; }
.icons-1 { background-position: 0 0; height: 42px; width: 28px; }
.icons-2, .other1 { background-position: 0 -42px; height: 42px; width: 28px; }
.other2 { background-position: 0 -42px; height: 42px; width: 28px; }
```
-----------------------------------我是华丽的分割线-------------------------------------------
## 移动端
移动端sprite的配置项如下：
移动端就复杂点了，因为要做成自适应，用上rem；至于详细介绍，详细设置原理参考[使用sass与compass合并雪碧图][1]博文。我对它的进行了完善，博文里面说生成的雪碧图得手动来改:
$bigWidth: 242px;
$bigHeight: 270px;
其实通过下面的方法可以获取雪碧图的大小.
ceil(image-width(sprite-path($icons)))
ceil(image-height(sprite-path($icons)))


最后这里的配置有两个地方需要注意的：
```
“$base-fonts-default: 100px; //基准数”，
```
这里是rem的换算基准，我demo的js是按照750px设计稿，1px=0.01rem来换算的规则，如果你的项目不是按这个换算，这个数值就得改变；
```
@for $i from 1 through 2 {
    .icons-#{$i} {
       @include sprite ($icons,$i)
    }
}
```
这里是遍历出.icons-的样式，首先所有图标按照数字顺序命名，我这里只有2个图标，所以是through 2；如果有几十个图标就只改这里就行了。如此，就能以最快的效率完成一个以sass基础的雪碧图配置来开发需求。
```
.icons-1{}
.icons-2{}
.icons-3{}
.icons-4{}
```
所有代码如下
```
@charset "UTF-8";
@import "compass";
@import "compass/utilities/sprites";
$icons: sprite-map("icons/*.png", $spacing: 8px, $layout: horizontal);
$base-fonts-default: 100px; //基准数
@function rem($px) {
    @if (type-of($px)=="number") {
        @return $px / $base-fonts-default * 1rem;
    }
    @if (type-of($px)=="list") {
        @if (nth($px, 1)==0 and nth($px, 2) !=0) {
            @return 0 nth($px, 2) / $base-fonts-default * 1rem;
        }
        @else if (nth($px, 1)==0 and nth($px, 2)==0) {
            @return 0 0;
        }
        @else if (nth($px, 1) !=0 and nth($px, 2)==0) {
            @return nth($px, 1) / $base-fonts-default * 1rem 0;
        }
        @else {
            @return nth($px, 1) / $base-fonts-default *1rem nth($px, 2) / $base-fonts-default * 1rem;
        }
    }
}
%same-style{
	background-image: sprite-url($icons);
	$bigWidth: ceil(image-width(sprite-path($icons)));
	$bigHeight: ceil(image-height(sprite-path($icons)));
	background-size: rem(($bigWidth, $bigHeight));
	background-repeat: no-repeat;
	display: inline-block;
}
@mixin sprite ($name) {
    width: rem(image-width(sprite-file($icons, $name)));
    height: rem(image-height(sprite-file($icons, $name)));
    background-position: rem(sprite-position($icons, $name));
    @extend %same-style;
}


@for $i from 1 through 2 {
    .icons-#{$i} {
       @include sprite ($i)
    }
}
.other{@extend .icons-1}


```
 


  [1]: http://www.cnblogs.com/xljzlw/p/4771103.html
