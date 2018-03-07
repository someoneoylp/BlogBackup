---
title: vue实现 移动端touch--点击、双指与拖拽指令
date: 2018-03-07 19:57:42
tags: vue
---

### 前言
用vue做移动端开发过程中，需要手势操作。因为vue-touch目前不支持vue2.0，所以自己写了几个手势。

### 实现功能
- 点击
- 双指缩放图片
- 移动

### 指令代码

```bash
myTouch.js >>
export default(Vue) => {
    Vue.directive('touch', {
        bind: function (el, binding, vnode) {
            let type = binding.arg; // 传入点击的类型
            let coordinate = {} // 记录坐标点的对象
            let timeOutTap;
            let timeOutLong;
            let scaleSize; // 缩放尺寸
            let displacement = {}; //移动的位移
            // 勾股定理计算距离
            function getDistance(bg, end){
                return Math.sqrt(Math.pow((end.x - bg.x),2 ) + Math.pow((end.y - bg.y),2 ));
            }
            // 点击开始的时候
            el.addEventListener('touchstart', function(e){
                // 获取第一个手指点击的坐标
                let x = e.touches[0].pageX;
                let y = e.touches[0].pageY; 
                // 如果点击的时间很长，那么点击的类型就是长按 --- longTouch
                timeOutLong = setTimeout(() => {
                    timeOutLong = 0;
                    if(type === 'longTouch'){
                         binding.value.func(binding.value.param)
                    }
                }, 2000)
                timeOutTap = setTimeout(() => {
                    timeOutTap = 0;
                    if(type === 'tap' && e.touches.length === 1){
                         binding.value.func(binding.value.param)
                    }
                }, 200)
                // 如果是两个手指，而且类型是缩放 --- scaleTocuh
                if(e.touches.length > 1 && type === 'scaleTouch'){
                    // 记录双指的间距长度
                    coordinate.start =  getDistance({
                        x: e.touches[0].pageX, 
                        y: e.touches[0].pageY,
                    },{
                        x: e.touches[1].pageX, 
                        y: e.touches[1].pageY,
                    })

                }
                // 如果是移动的话，还要记录下来最开始的位置,只能一个手指位移
                if(type === 'slideTouch' && e.touches.length == 1){
                    // debugger
                    displacement.start = {
                        x: e.touches[0].pageX,
                        y: e.touches[0].pageY,
                    }
                }
            }, false)
            el.addEventListener('touchmove', function(e){
                clearTimeout(timeOutTap)
                clearTimeout(timeOutLong)
                timeOutTap = 0; timeOutLong = 0;
                // 如果是缩放类型
                if(type == 'scaleTouch' && e.touches.length === 2){
                    // 计算移动过程中的两个手指的距离
                    coordinate.stop = getDistance({
                        x: e.touches[0].pageX, 
                        y: e.touches[0].pageY,
                    },{
                        x: e.touches[1].pageX, 
                        y: e.touches[1].pageY,
                    })
                    // 设置缩放尺寸
                    scaleSize = (coordinate.stop / coordinate.start)  - 1;
                    // 这里设置图片不能无限放大与缩小
                    // 这里设置放大缩小速度慢一点，所以 /4一下
                    binding.value.func(scaleSize / 2, false)
                }
                // 如果是移动类型
                if(type == 'slideTouch' && e.touches.length === 1 ){
                    displacement.end = {
                        x: e.changedTouches[0].pageX,
                        y: e.changedTouches[0].pageY,
                    }
                    binding.value.func({x:displacement.end.x - displacement.start.x, y: displacement.end.y - displacement.start.y, is_endMove : false})
                }
            }, false)
            el.addEventListener('touchend', function(e){
                if(type === 'scaleTouch'){
                    binding.value.func(0, true)
                }
                if(type === 'slideTouch'){
                    binding.value.func({x:0, y: 0, is_endMove : true})
                }
            }, false)
        }
    })
}
```
### 使用方法
```bash
html:
 <img :src="picUrl" class="modal_content"
            v-touch:scaleTouch = "{func: scalePic, param: ''}"
            v-touch:slideTouch = "{func: movePic, param: ''}"
/>

vue:
main.js >>
import myTouch from './myTouch'
myTouch(Vue)

app.vue >>
     data:function () {
      return{
          baseLeft : 0,
          baseTop: 0,
          bodyWidth: document.body.clientWidth,
          ele: null, // 不能在这里设置， dom还没有生成
      }
  },
```

### 放大功能实现
```bash
scalePic: function(param, is_endScale){
      this.ele =document.getElementsByClassName('modal_content')[0]; // 这个应该在图片显示的时候设置
      let nodeStyle = this.ele.style.transform;
      let changeSize = nodeStyle ?  parseFloat(nodeStyle.slice(nodeStyle.indexOf('scale')+6)) : 0;
      if(is_endScale){
        // 缩放图片结束，要重新计算定位
        this.setMaxdisp(changeSize,parseInt(this.ele.style.marginLeft), parseInt(this.ele.style.marginTop), 'scale')
        return 
      }
      if(nodeStyle){
        // 如果存在的话，就说明已经设置了，scale已经改变了
        let currScaleSize = changeSize + param; 
        currScaleSize > 3 ? currScaleSize = 3 : null
        currScaleSize < 1 ? currScaleSize = 1 : null
        this.ele.style.transform = 'translate(-50%, -50%) scale('+currScaleSize+','+currScaleSize+')';
      }else{ // 如果不存在，就说明是第一次设置
          let scale = param + 1 
          this.ele.style.marginLeft =  '0px';
          this.ele.style.marginTop  = '0px';
          this.ele.style.transform = 'translate(-50%, -50%) scale('+scale+','+scale+')';
      }
    }
```

### 移动功能
```bash
movePic: function(param){
     if(param.is_endMove){ // 每次移动松开手指的时候要下次移动的基础坐标
        this.baseLeft = parseInt(this.ele.style.marginLeft.slice(0, -2));
        this.baseTop = parseInt(this.ele.style.marginTop.slice(0, -2));
      }else{
        let nodeStyle = this.ele.style.transform 
        if(nodeStyle){ // 有这个就表示应该是移动
          let currScaleSize = parseFloat(nodeStyle.slice(nodeStyle.indexOf('scale')+6))
          this.setMaxdisp(currScaleSize,this.baseLeft+ param.x, this.baseTop + param.y, 'move')
        }
      }
    }
```

### 计算最大位移
```bash
setMaxdisp:function(changeSize, changeX, changeY, type){
      // 计算最大位移
      // naturalWidth ： 是图片原始的宽度，通过比例可以计算出当前图片在页面的高度
      let picHeight =  this.bodyWidth  / (this.ele.naturalWidth / this.ele.naturalHeight); 
      let maxTop = ( picHeight * changeSize - window.innerHeight) /2 
      let maxLeft = this.bodyWidth / 2 * (changeSize - 1) 
      if(changeX >= maxLeft){
        this.ele.style.marginLeft = maxLeft + 'px'
      }else if(changeX < -maxLeft){
        this.ele.style.marginLeft = -maxLeft + 'px'
      }else if(type==='move'){
        this.ele.style.marginLeft =changeX +'px'; 
      }
      // 如果图片当前尺寸大于屏幕尺寸，可以移动
      if(maxTop > 0){
        if(changeY >= maxTop){
          this.ele.style.marginTop = maxTop + 'px';
        }else if(changeY < -maxTop){
          this.ele.style.marginTop = -maxTop + 'px'
        }else if(type==='move'){
          this.ele.style.marginTop = changeY+'px';
        }
      }else if(type==='move'){
        this.ele.style.marginTop = 0 +'px'; 
      }
    }
```

### 遇到的问题
#### 双指与单指容易混淆
在双指放大缩小的时候，两个手指松开时间不同，页面就会在两个手指松开的时候判断为点击事件。 这个还没有解决
    
#### 计算图片位移程度
图片不能随意的位移。应该是在一定范围内去位移的，所以写了一个计算最大位移的函数，图片位移距离应该是图片刚好能看到的程度。通过图片原始的宽高，以及页面当前的宽度比例可以计算出当前图片在页面的高度。然后计算出最大位移

#### 每次移动的基础位置
图片移动或者缩小懂应该是在一个基础数据上去增加减少，如果没有设置这个基础数据，会导致每次都是从1的比例来移动以及缩小，所以每次移动以及放大缩小图片之后，都要记录下来改变之后图片的参数，下次改变图片的时候都是在这个参数基础上去改变的。

