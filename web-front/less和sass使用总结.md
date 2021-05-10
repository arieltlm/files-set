[toc]

# 1.不让stylelint校验某一句

```css
/* stylelint-disable */
& > div {
  margin-bottom: 8px;
}
/* stylelint-enable */
```

# 2. less

## 1.less中calc加变量

```less
@space-lg: 24px;
width: calc(～'100% - 32px - @{space-lg}');
```

## 2.变量插值

```less
// class名
@my-selector: banner;

// Usage
.@{my-selector} {
  margin: 0 auto;
}
// 输出：
.banner {
  margin: 0 auto;
}
```

```less
// 值-图片路径
@images: "../img";

// Usage
body {
  color: #444;
  background: url("@{images}/white-sand.png");
}
```

## 3.循环

```less
@i:8;
.loop(@i)when(@i>0){
  .loop((@i - 1));
  .cell-bg@{i} {background: url('../images/cell-bg@{i}.png') no-repeat;}
}
.loop(@i);

// 输出
.cell-bg1 {
  background: url('../images/cell-bg1.png') no-repeat;
}
.cell-bg2 {
  background: url('../images/cell-bg2.png') no-repeat;
}
.cell-bg3 {
  background: url('../images/cell-bg3.png') no-repeat;
}
.cell-bg4 {
  background: url('../images/cell-bg4.png') no-repeat;
}
.cell-bg5 {
  background: url('../images/cell-bg5.png') no-repeat;
}
```

```less
.generate-columns(4);

.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);
  }
  .generate-columns(@n, (@i + 1));
}
// 输出
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



# 3.scss

## 1.scss中calc加变量

```scss
$space-lg:24px;
width: calc(100% - 32px - #{$space-lg});
```

## 2.循环

* for循环

```css
$listemColor:#00a8ff #0cdddb #ffb400 #ffb400;
@for $i from 1 through 5 { 
    &:nth-child(#{$i}){
        em{
            color:nth($listemColor,$i);
        }
        .right-list-item{ 
            background: url('../images/core/personas/tradehold-icon#{$i}.png') no-repeat 100% 100%;
            background-size: 100% 100%;
        } 
    } 
}
```

* each 循环

```css
@each $member in a, b, c, d {
.#{$member} {
　　　background-image: url("/image/#{$member}.jpg");
    }
}
```

* while 循环
```css
$i: 6;

@while $i > 0 {
　.item-#{$i} { width: 2em * $i; }
　　$i: $i - 2;
}
```

## 3.scss可以直接做加减乘除

```scss
$leftAndWidth: 30px;
 
.left-and {
    position: absolute;
    top: 0;
    width: $leftAndWidth;

    span{
      ...
      width: $leftAndWidth - 5;
      height: $leftAndWidth - 5;
      line-height: $leftAndWidth - 5;
    }

    &:before{
      ...
      left:$leftAndWidth / 2 - 3;

    }
}
```

## 4.scss中在媒体查询中的定义变量

```scss
// 无用
$last-div-height: 160px;
@media (max-width:1440px){
	$last-div-height: 100px;
}
```

在scss中上面的是不起作用的，拿到的总是100px； 由于触发@media screen and (max-width: 1440px)发生在客户端。

只有SASS抓住样式表中包含$base_width变量的所有规则和属性并相应地复制/更改它们，才能实现预期结果。

```scss
$last-div-height: 160px;
$last-div-1440-height: 100px;
	
div{
	height:$last-div-height;
}
@media (max-width:1440px){
	div{
		height:$last-div-1440-height;
	}
}
```

