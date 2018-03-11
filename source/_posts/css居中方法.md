---
title: css居中方法
date: 2017-10-25 20:00:09
tags: css
---
### 第一种：通过margin负值
``` bash 
<div class="one"></div>
```
<!-- more -->
```bash
.one{
    position: absolute;
    width: 200px;
    height: 200px;
    top:  50%;
    left: 50%;
    margin-left: -100px;
    margin-right: -100px;
    background: green;
}
```
 优点：
 基本浏览器都能兼容

 缺点：
必须要固定宽高
### 第二种：通过margin:auto
```bash
<div class="two"></div>
```
```bash
.two{
    position: absolute;
    width: 100px;
    height:100px;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
```
以上两种方法都可以把absolute换成fixed,注意，fixed在ie下不支持

### 第三种：table-cell
```bash
<div class="inner">
    <div class="foo"></div>
</div>
```
```bash
.inner{
    width: 100px;
    height: 100px;
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
.foo{
    display: inline-block;
    width: 50%;
    height: 50%;
}
```
设置了table-cell之后，父元素就变成了一个单元格
[关于使用table-cee布局][1]


  [1]: http://www.jianshu.com/p/8aa3f1030908

### 第四种:行内元素居中
```bash
<div class="four">
    内容居中
</div>
```
```bash
.four{
    width: 100px;
    height: 100px;
    line-height: 100px;
    text-align: center;
}
```
这种方法只能居中行内元素。常用于文字对其居中

### 第五种：transform居中
```bash
<div class="five"></div>
```
```bash
.five{
    position: absolute;
    top: 50%;
    height: 50%;
    transform: translate(-50%, -50%);
}
```
好处就是不可不用定义宽高，但是对于不兼容css3的浏览器没有作用

### 第六种：伪类居中
```bash
<div class="six">
    <div class="content"></div>
</div>
```
```bash
.six{
    position:aabsolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    text-align: center;
    }
.six:before{
    content: '';
    display: inline-block;
    vertical-align: middle;
    height: 100%;
    }
.six .content{
    display: inline-block;
    vertical-align: middle;
    width: 40px;
    height: 40px;
    line-height: 40px;
    }
  ```

### 第八种：flex布局
```bash
<div class="eight">
    <div class="content"></div>
</div>
```
```bash
.eight{
    display: flex;
    align-items: center;
    justify-content: center;
}
```
同样，会存在浏览器兼容问题