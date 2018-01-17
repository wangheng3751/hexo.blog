---
title: 原生js+css 实现手机滑动显示隐藏按钮功能
date: 2018-01-17 11:37:59
categories: 前端
tags: HTML
---

前言：最近做项目的过程中遇到需要在列表上可以有“修改”和“删除”按钮的需求，但由于在移动端，手机界面较小，在显示了其他必须内容后再直接显示两个按钮的话会导致界面很臃肿，难以适应各个机型，所以考虑采用模拟一些手机APP的做法，首先隐藏按钮，在用户按住某条数据左滑或者右滑时显示按钮。由于本人前端水平也很一般，只能各种百度搜罗文档之后终于实现了该功能，在此做下记录和总结，方便自己以后查询，同时供同样需要的朋友参考。

>实例只实现了左滑显示，有右滑需求可仿照

效果预览：![](http://upload-images.jianshu.io/upload_images/9814928-6c02cfa5677b312c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 1.HTML代码
```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <title>侧滑</title>
        <link rel="stylesheet" href="index.css"/>
        <script type='text/javascript' src='index.js' charset='utf-8'></script>
    </head>
    <body> 
    <div class='box'>
        <div class="swipe-container" >      
            <div class="item-container">
                <img src="resource/logo.jpg"/>
                <div class="content">
                    <div class="title">这里是标题</div>
                    <div class="desc">这里是简单描述
                        <span>2018-01-17</span>
                    </div>
                </div>
                <div class="allow-right"></div>            
            </div> 
            <div class="action action-edit">编辑</div>
            <div class="action action-delete">删除</div>
        </div>

        <div class="swipe-container">      
            <div class="item-container">
                <img src="resource/logo.jpg"/>
                <div class="content">
                    <div class="title">这里是标题</div>
                    <div class="desc">这里是简单描述
                        <span>2018-01-17</span>
                    </div>
                </div>
                <div class="allow-right"></div>            
            </div> 
            <div class="action action-edit">编辑</div>
            <div class="action action-delete">删除</div>
        </div>

        <div class="swipe-container">      
            <div class="item-container">
                <img src="resource/logo.jpg"/>
                <div class="content">
                    <div class="title">这里是标题</div>
                    <div class="desc">这里是简单描述
                        <span>2018-01-17</span>
                    </div>
                </div>
                <div class="allow-right"></div>            
            </div> 
            <div class="action action-edit">编辑</div>
            <div class="action action-delete">删除</div>
        </div>
    </div>
    </body>
</html>
```
## 2.用到的一些CSS
```
body{background-color: #eee;margin:0;overflow-y:scroll;}
/*必须overflow-x:hidden 否则网页会整体出现横向滚动*/
.box{overflow-x:hidden;width:100%}
/*内容宽度占据整个页面宽度 使按钮被隐藏*/
.item-container{width:100%;padding:0.5rem;background-color: #fff;display: flex;align-items: center;height: 3.5rem}
.item-container img{height: 3.5rem;width: 3.5rem}
.content{flex:1;margin: 0 1rem}
.title{font-size: 1rem;color: #000;margin-bottom:0.5rem}
.desc{font-size: 0.9rem;color: #999}
.desc span{float: right}
.allow-right{width:2rem;height: 3.5rem;background:transparent url(resource/allow_right.png) no-repeat;background-size:1.5rem 1.5rem;background-position: center}
.action-delete{background:#C95454}
.action-edit{background:#00BCD4}
/*-webkit-transition:all 0.3s linear;transition:all 0.3s linear;使浏览器出现滑动效果
width: 140%;内容占100% 两个按钮各占20% ，根据按钮数量和宽度调整
*/
.swipe-container{ margin-bottom:0.5rem;width: 140%;display: flex;-webkit-transition:all 0.3s linear;transition:all 0.3s linear;}
/*width:20%; 和swipe-container width: 140%;相对应*/
.swipe-container .action{width:20%;text-align:center;color:#fff;padding:0.5rem 0;line-height: 3.5rem;}
/*
使元素向左滑动之后显示被隐藏的按钮 此处不知道原理 写40%不行 
大概是因为此处的宽度是以swipe-container的总宽度为100%来计算，按钮的宽度20%则时以网页的宽度为100%来计算的，即 ：100*(20%)=140*(14.25%)
根据按钮数量和宽度调整
*/
.swipeleft{transform:translateX(-28.5%);-webkit-transform:translateX(-28.5%);}
```
## 3.JS实现
```
 window.onload=function(){
    setSlide();
    var elems=document.getElementsByClassName("action-delete");
    for(var i=0;i<elems.length;i++){
        elems[i].addEventListener("click", function(){     
            var r=confirm("确定要删除该记录吗?")
            if (r){
              alert("呵呵呵");
            }else{
              alert("哈哈哈");
            }
        });
    }
 };
 
 function setSlide() {
    //侧滑显示删除按钮
    var expansion = null; //是否存在展开的list
    var container = document.querySelectorAll(".swipe-container");
    for (var i = 0; i < container.length; i++) {
        var x, y, X, Y, swipeX, swipeY;
        container[i].addEventListener('touchstart', function (event) {
            x = event.changedTouches[0].pageX;
            y = event.changedTouches[0].pageY;
            swipeX = true;
            swipeY = true;
            if (expansion) { //判断是否展开
                //如果展开则收起 以下代码取消注释后则滑动开始立即收起 注释后则向右滑动一定距离后收起隐藏按钮
                //方法一.
                //removeClass(this,"swipeleft");
                //方法二 需引入jquery
                //$(this).removeClass("swipeleft");
            }
        });
        container[i].addEventListener('touchmove', function (event) {
            X = event.changedTouches[0].pageX;
            Y = event.changedTouches[0].pageY;
            // 左右滑动
            if (swipeX && Math.abs(X - x) - Math.abs(Y - y) > 0) {
                // 阻止事件冒泡
                event.stopPropagation();
                if (X - x > 10) { //右滑收起
                    event.preventDefault();
                    removeClass(this,"swipeleft");
                    //$(this).removeClass("swipeleft");
                }
                if (x - X > 10) { //左滑展开
                    event.preventDefault();
                    //$(this).addClass("swipeleft");
                    addClass(this,"swipeleft");
                    expansion = this;
                }
                swipeY = false;
            }
            // 上下滑动
            if (swipeY && Math.abs(X - x) - Math.abs(Y - y) < 0) {
                swipeX = false;
            }
        });
    }
}

function hasClass(elem, cls) {
    cls = cls || '';
    if (cls.replace(/\s/g, '').length == 0) return false;
    return new RegExp(' ' + cls + ' ').test(' ' + elem.className + ' ');
  }
   
  function addClass(ele, cls) {
    if (!hasClass(ele, cls)) {
      ele.className = ele.className == '' ? cls : ele.className + ' ' + cls;
    }
  }
   
  function removeClass(ele, cls) {
    if (hasClass(ele, cls)) {
      var newClass = ' ' + ele.className.replace(/[\t\r\n]/g, '') + ' ';
      while (newClass.indexOf(' ' + cls + ' ') >= 0) {
        newClass = newClass.replace(' ' + cls + ' ', ' ');
      }
      ele.className = newClass.replace(/^\s+|\s+$/g, '');
    }
  }
```
## 4.在线效果预览
预览地址：https://wangheng3751.github.io/assets/my-examples/sideslipping/index.html

或扫一扫下方二维码预览
![](https://github.com/wangheng3751/my-resources/blob/master/images/slide-qrcode.png?raw=true)

## 5.源码地址
[Github访问地址](https://github.com/wangheng3751/useful-example.front-end/tree/master/examples/sideslipping) :https://github.com/wangheng3751/useful-example.front-end/tree/master/examples/sideslipping