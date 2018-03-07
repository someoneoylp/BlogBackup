---
title: react创建组件的三种方法以及绑定this的三种方法
date: 2018-03-07 20:04:30
tags: react
---
1.函数定义：无状态
    这种组件只负责根据传入的props来展示，不涉及到要state状态的操作
 只有一个render方法的组件类，用于分割原本庞大的组件，如果组件不涉及state或者生命周期，尽可能使用这种方法
 ```bash
function HelloComp(props){
        return <div>{props.name}</div>
}
```
可读性好，减少大量荣誉代码，组件不会被实例化（不需要分配多余的内存），整体渲染性能得到提升
不能访问this对象，没有生命周期，不会有副作用，就像函数式编程一样，相同的props输入，输出的结构总是一样的，它只能访问输入的props


2.es5的React.createClass ，有状态组件
 ```bash
let InputControl = React.createClass({
    prototype:{},
    defaultProps:{},
    ….
    render: function(){}
})
```
会自动绑定函数方法，每一个成员函数的this 都与React自动绑定，支持mixins

3.es6的React.Component，有状态组件
更好的代码复用，成员函数不会自动绑定this,不支持mixins

绑定this的方法：
 ```bash
1.<button onclick = {this.handleClick.bind(this)}>
```
2.构造函数内绑定：
 ```bash
    constructors(props){
            super(props)
            this.handleClick = this.handleClick.bind(this)
} 
```
只要绑定一次
3.箭头函数
 ```bash
<button onclick = {() => {this.handleClick()}}>
```
如果要移除监听事件，箭头函式又总是匿名的，可以使用以下方法：
 ```bash
handleClick = (e) => {
        ...
}
<button onclick = {this.handleClick}>
```











