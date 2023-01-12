---
title: CSS 面试题
excerpt: CSS常见面试题总结（持续更新中)
date: 2021-02-01 16:43:41
tags: [css , 面试]
categories: [面试]
---
# CSS3 面试题



### 盒模型宽度计算

> offsetwidth = （内容宽度 + 内边距 + 边框），无外边距
>
> 使用 box-sizing : border-box 可以使整个宽度 = width的宽度

### margin纵向重叠问题

> - 相邻元素的 margin-top 和 margin-bottom 会发生重叠
> - 空白内容的 <p></p> 也会重叠

### 什么是BFC，如何应用

> Block format context，块级格式化上下文，是一块独立渲染区域，内部元素的渲染不会影响边界以外的元素
>
> **形成BFC的常见条件：**
>
> - float 只要设置的不是 none，都会形成BFC
> - position: absolute / fixed
> - overflow: 只要不是 visiable，都会形成BFC
> - display 是 flex / inline-block 等
>
> **BFC的常见应用**
>
> * 使用BFC，在父级添加overflow属性清除浮动（解决脱离文档流的问题)
> 
HTML
```html
    <div class="container bfc">
        <img src="https://files.cnblogs.com/files/moqiutao/a.gif" alt="" class="left">
         <p class="bfc">description</p>
    </div>
 ```
CSS
 ```css
    .container {
        background-color: #f1f1f1;
    }
    .left {
        float: left;
    }
    .bfc {
        overflow: hidden; /* 触发元素 BFC */
     }
```
>  存在问题：内容增多的时候容易造成不会自动换行导致内容被隐藏掉，无法显示要溢出的元素（**不推荐使用**）
>
> * 推荐使用after伪元素
```html
<div class="container clearfix">
    <img src="https://files.cnblogs.com/files/moqiutao/a.gif" alt="" class="left">
    <p class="">description</p>
</div>
>   > ```

CSS
```css
    .clearfix:after {
        /*伪元素是行内元素 正常浏览器清除浮动方法*/
        content: "";
        display: block;
        height: 0;
        clear: both;
        visibility: hidden;
     }
    .clearfix {
        *zoom: 1;
        /*ie6清除浮动的方式 *号只有IE6-IE7执行，其他浏览器不执行*/
    }
 ```

### 如何实现圣杯布局和双飞翼布局（float布局）

#### 圣杯布局和双飞翼布局的目的

- 三栏布局，中间一栏最先加载和渲染（内容最重要）
- 两侧内容固定，中间内容随着宽度自适应
- 一般用于PC网页

#### 圣杯布局和双飞翼布局的总结

- 使用float布局。
- 两侧使用margin负值，以便和中间内容横向重叠
- 防止中间内容被两侧覆盖，一个用 padding 一个用 margin
- 圣杯布局两边通过 padding 来留白
- 双飞翼布局两边通过 margin 来留白

#### 手写clearfix

```css
.clearfix:after {
	content: '';
  display: table;
  clear: both;
}
```

#### flex实现一个三点的色子

> flex-direction	主轴方向
>
> justify-content	主轴水平对齐方式
>
> align-items	垂直对齐方式
>
> flex-wrap	换行
>
> align-self	交叉轴居中对齐

### CSS定位

> absolute 和 relative 定位的依据
>
> - Relative 依据自身定位
>
> - absolute 依据最近一层的*<u>**定位元素**</u>*定位
    >
    >   > 定位元素：absolute relative fixed body

### 居中对齐的实现方式

- 水平居中

  > inline元素:	text-alian: center
  >
  > block元素:	margin:auto
  >
  > absolute元素:	left:50% 	+	 margin-left: 负值

- 垂直居中

  > inline元素:	line-height 的值等于 height 的值
  >
  > absolute元素:
  >
  > - top: 50%  +  margin-top 负值
  > - transform(-50%, -50%) （css3的属性，完美解决不需要知道宽度的问题）
  > - top,left,bottom,right = 0 + margin:auto  (兼容性最好，也无需知道宽度)

### CSS-图文样式

- line-height	如何继承

  > line-height	设置为百分比，则使用父标签的高度来计算百分比
  >
  > ​						设置为具体型，则使用自己的高度来计算

### CSS-响应式

- rem是什么

  > px ，绝对长度单位
  >
  > em ，相对长度单位，相对于父元素
  >
  > rem，相对长度单位，相对于根元素，常用语响应式

- 响应式布局的常用方案

  > Media-query ，根据不同的屏幕来设置根元素的 font-size
  >
  > rem, 基于根元素的
  >
  > vw/vh
  >
  > - rem弊端：“阶梯”性，媒体查询的三个宽度阶梯
  > - vh 网页视口高度的 1/100
  > - vw 网页视口宽度的 1/100
  > - Vmax 取 vh\vw两者最大值, vmin 取两者最小值

#### 网页视口尺寸

- Window.screen.height	屏幕高度
- window.innerHeight      网页视口高度
- document.body.clientHeight  body高度
