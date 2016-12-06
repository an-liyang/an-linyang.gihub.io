# less用法

## 变量定义
  + @ 变量名 如 @color：#f60  直接使用 div{ color:@ color}
  + 甚至可以用变量名定义为变量:

  - @fnord: "I am fnord.";
  - @var: 'fnord';
  - content: @@var;
注意less中的变量是常量，所有只能设置一个即可
---
## 通用类class使用
### .class
然后在另一个class中去调用这些属性. 下面有这样一个class:
```js
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```
那如果我们现在需要在其他class中引入那些通用的属性集，那么我们只需要在任何class中像下面这样调用就可以了:
```js
#menu a {
  color: #111;
  .bordered;
}
.post a {
  color: red;
  .bordered;
}
```

任何 CSS class, id 或者 元素 属性集都可以以同样的方式引入.
---
## 带参数混用性

1. .border(@radius){
border-radius:@ radius;	
}
调用的时候直接.border(6px)就行

也可以直接赋值.border-radius (@radius: 5px)，直接调用即可；
2. .函数名(参数1，参数2){};
###
你也可以定义不带参数属性集合,如果你想隐藏这个属性集合，不让它暴露到CSS中去，但是你还想在其他的属性集合中引用，你会发现这个方法非常的好用:
```js
.wrap () {
  text-wrap: wrap;
  white-space: pre-wrap;
  white-space: -moz-pre-wrap;
  word-wrap: break-word;
}

pre { .wrap }

输出:

pre {
  text-wrap: wrap;
  white-space: pre-wrap;
  white-space: -moz-pre-wrap;
  word-wrap: break-word;
}
```
## @arguments 变量

### 
```js
.box-shadow (@x: 0, @y: 0, @blur: 1px, @color: #000) {
  box-shadow: @arguments;
  -moz-box-shadow: @arguments;
  -webkit-box-shadow: @arguments;
}
.box-shadow(2px, 5px);

将会输出:
box-shadow: 2px 5px 1px #000;
```
## 模式匹配和导引表达式

```js
如果想让.mixin根据不同的@switch值而表现各异，如下这般设置：

.mixin (dark, @color) {
  color: darken(@color, 10%);
}
.mixin (light, @color) {
  color: lighten(@color, 10%);
}
.mixin (@_, @color) {
  display: block;
}

现在，如果运行:

@switch: light;

.class {
  .mixin(@switch, #888);
}

```

`@switch 表示多种选择模式和switch ……case 选择匹配的语句执行
@_ 表示匹配任意参数即可，可以理解为基本属性设置`
@media 表示媒体查询
```js
@media: mobile;

.mixin (@a) when (@media = mobile) { ... }
.mixin (@a) when (@media = desktop) { ... }

```
具体实现如下：

    第一个混合定义并未被匹配，因为它只接受dark做为首参
    第二个混合定义被成功匹配，因为它只接受light
    第三个混合定义被成功匹配，因为它接受任意值


## 引导
```js
.mixin (@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin (@a) {
  color: @a;
}
```

除去关键字`true`以外的值都被视示布尔假：

.class {
  .truth(40); // Will not match any of the above definitions.
}

导引序列使用逗号‘,’—分割，可以传多个条件，当且仅当所有条件都符合时，才会被视为匹配成功。

相当于||运算符

.mixin (@a) when (@a > 10), (@a < -10) { ... }

  + 导引中可用的全部比较运算有： > >= = =< < 。此外，关键字true只表示布尔真值，下面两个混合是相同的：
  + 如果想基于值的类型进行匹配，我们就可以使用is*函式：
.mixin (@a, @b: 0) when (isnumber(@b)) { ... }
.mixin (@a, @b: black) when (iscolor(@b)) { ... }

下面就是常见的检测函式：

    iscolor
    isnumber
    isstring
    iskeyword
    isurl
  + and 和 not  
.mixin (@a) when (isnumber(@a)) and (@a > 0) { ... }

使用not关键字实现或条件

.mixin (@b) when not (@b > 0) { ... }

## 嵌套规则

### LESS 可以让我们以嵌套的方式编写层叠样式. 让我们先看下下面这段 CSS:
```js
#header        { color: black;
  .navigation  { font-size: 12px }
  .logo        { width: 300px;
    &:hover    { text-decoration: none }
  }
}

```
#### 注意 `& 符号`的使用—如果你想写`串联选择器`(表示本来就是它本身)，而不是写后代选择器，就可以用到&了. 这点对伪类尤其有用如 :hover 和 :focus.
li{};li:hover{};直接写成li{ &；hover{}};

## 运算
```js
@base: 5%;
@filler: @base * 2;
@other: @base + @filler;

color: #888 / 4;
background-color: @base-color + #111;
height: 100% / 2 + @filler;
```
---
## Color 函数

1. 你还可以提取颜色信息:
```js
hue(@color);        // returns the `hue` channel of @color
saturation(@color); // returns the `saturation` channel of @color
lightness(@color);  
```
2. lighten(@color, 10%);     // return a color which is 10% *lighter* than @color
darken(@color, 10%);      // return a color which is 10% *darker* than @color

saturate(@color, 10%);    // return a color 10% *more* saturated than @color
desaturate(@color, 10%);  // return a color 10% *less* saturated than @color

fadein(@color, 10%);      // return a color 10% *less* transparent than @color
fadeout(@color, 10%);     // return a color 10% *more* transparent than @color
fade(@color, 50%);        // return @color with 50% transparency

spin(@color, 10);        
spin(@color, -10);        // return a color with a 10 degree smaller hue than @color
  - 我们可以进一步探讨颜色模型并且创建一个三元色结构。“三元”，顾名思义，是由三种颜色组成。因此，我们将色轮分成三部分，并且用函数`spin()`来设置旋转的度数：色轮
spin(颜色，角度)：spin(#f60,120);

mix(@color1, @color2);    // return a mix of @color1 and @color2
  - 混合色

　　创建配色方案的另一种方式是对两种颜色进行混合，也许就像你上小学美术课时学到的那样。例如，如果我们把红色与黄色混合，我们会得到橙色。在LESS中，我们可以使用mix()函数实现同样的效果:
```js
div{
color:min(red,yellow);
}
输出橙色
```
3. @new: hsl(hue(@old), 45%, 90%);

4. @new 将会保持 @old的 色调, 但是具有不同的饱和度和亮度.

## 命名空间 ##

+ 有时候，你可能为了更好组织CSS或者单纯是为了更好的封装，将一些变量或者混合模块打包起来, 你可以像下面这样在#bundle中定义一些属性集之后可以重复使用:

```html
#bundle {
  .button () {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover { background-color: white }
  }
  .tab { ... }
  .citation { ... }
}

你只需要在 #header a中像这样引入 .button:

#header a {
  color: orange;
  `#bundle > .button;`
}

```
这里是具体调用，通过`xXX > YYY`方式进行调用，本人觉得用 `-〉`更可靠，起码长得不像亲子选择器

这里应该只`包含方法`，否则里面的合法语句会生成出来

#### Extend
我们可以使用extend伪类来实现样式的继承使用。

```js
.public {
        width: 100px;
        height: 100px;
    }

    nav ul {
        &:extend(.public);
        list-style: none;
    }

    //->编译为CSS的结果
    .public, nav ul {
        width: 100px;
        height: 100px;
    }

    nav ul {
        list-style: none;
    }
```


### Loops

在Less中，混合可以调用它自身。这样，当一个混合递归调用自己，再结合Guard条件表达式，就可以写出循环结构。使用递归循环最常见的情况就是生成`栅格系统的CSS`
```js
.generate(4);
.generate(@n,@i:1) when (@i<=@n){
  .column-@{i}{
  width:(@i*100%/@4)
  }
  .generate(@n,@i+1);
}

输出css
.column-1 {
        width: 25%;
    }

    .column-2 {
        width: 50%;
    }

    .column-3 {
        width: 75%;
    }

    .column-4 {
        width: 100%;
    }
```

## 作用域

```js
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}

#footer {
  color: @var; // red  
}
```

