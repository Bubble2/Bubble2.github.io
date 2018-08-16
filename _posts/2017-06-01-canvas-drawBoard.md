---
layout:     post
title:      "简易的canvas画板"
date:       "2017-06-01"
tags:       "canvas"
author:     "guozhaodong"
---

# 简易的canvas画板

没事仿照windows画板工具用canvas实现了一个简易版的画板。

<a href="https://codepen.io/Bubble2/pen/qyzbEM" target="_blank">demo预览</a>

html：
```html
<!doctype html>
<html>
    <head>
        <meta charset="utf-8">
        <title>画板</title>
        <link rel="stylesheet" href="css/style.css">
    </head>
    <body>
        <div class="toolbar js-toolbar">
            <div class="toolbar-box">
                <div class="toolbar-main">
                    <div class="shape-box">
                        <ul class="shape-lst js-tool-lst">
                            <!-- <li data-tool="eraser">橡皮擦</li> -->
                            <li data-tool="eraserAll">清除全部</li>
                        </ul>
                    </div>
                </div>
                <div class="toolbar-title">工具</div>
            </div>
            <div class="toolbar-split"></div>
            <div class="toolbar-box">
                <div class="toolbar-main">
                    <div class="shape-box">
                        <ul class="shape-lst js-shape-lst">
                            <li data-tool="line" class="selected">直线</li>
                            <li data-tool="rect">矩形</li>
                            <li data-tool="arc">圆形</li>
                        </ul>
                    </div>
                    <div class="shape-chose">
                        <ul>
                            <li>轮廓</li>
                            <li>填充</li>
                        </ul>
                    </div>
                </div>
                <div class="toolbar-title">形状</div>
            </div>
            <div class="toolbar-split"></div>
            <div class="toolbar-box">
                <div class="toolbar-main toolbar-lineWidth-main">
                    <ul class="line-width-lst js-line-width-lst">
                        <li class="line-width-1 selected" data-lineWidth="1"><span></span></li>
                        <li class="line-width-2" data-lineWidth="2"><span></span></li>
                        <li class="line-width-3" data-lineWidth="3"><span></span></li>
                        <li class="line-width-4" data-lineWidth="4"><span></span></li>
                        <li class="line-width-5" data-lineWidth="5"><span></span></li>
                    </ul>
                </div>
                <div class="toolbar-title">线条</div>
            </div>
            <div class="toolbar-split"></div>
            <div class="toolbar-box">
                <div class="toolbar-main">
                    <ul class="color-lst js-color-lst">
                        <li class="black selected" data-color="#000"><span></span></li>
                        <li class="red" data-color="#f00"><span></span></li>
                        <li class="blue" data-color="#00f"><span></span></li>
                        <li class="green" data-color="#0f0"><span></span></li>
                        <li class="yellow" data-color="#ff0"><span></span></li>
                    </ul>
                </div>
                <div class="toolbar-title">颜色</div>
            </div>
        </div>
        <canvas id="canvas" class="canvas" width="800" height="600"></canvas>
        <script src="js/drawBoard.js"></script>
        <script>
            window.onload=function(){
                var drawBoard=new DrawBoard();
            }
        </script>
    </body>
</html>
```

style.css：
``` css
*{
    margin:0;
    padding:0;
}
body{
    background:#c9d3e2;
}
ul,li{
    list-style:none;
}
.canvas{
  border:1px solid #ddd;
  background:#fff;
  box-shadow:5px 5px 5px rgba(9,93,224,.1);
  cursor:crosshair;
}
.toolbar{
    padding:7px 0;
    background:-webkit-linear-gradient(top,#fbfdff,#dce7f5);
    border-top:1px solid #bac9db;
    border-bottom:1px solid #bac9db;
    box-shadow:inset 0 -1px 1px #e5f0fb,inset 0 -1px 1px #cedbeb;
    overflow:hidden;
}
.toolbar-box{
    float:left;
    padding:0 6px;
}
.toolbar-main{
    height:80px;
    overflow:hidden;
}
.shape-box{
    float:left;
    width:150px;
    height:58px;
    border:1px solid #aabbd2;
    background:#f3f7fc;
    overflow-y:auto;
}
.shape-chose{
    float:left;
    margin-left:10px;
    width:50px;
}
.shape-chose li{
    color:#489de4;
    line-height:20px;
}
.shape-lst{
    overflow:hidden;
}
.shape-lst li{
    float:left;
    height:20px;
    line-height:20px;
    padding:0 10px;
    color:#1a6aab;
    cursor:default;
}
.shape-lst li:hover{
    background:#fbe7c2;
}
.shape-lst .selected,
.shape-lst .selected:hover,
.shape-lst li:active{
    background:#ffc762;
    box-shadow:inset 0 0 15px rgba(60,40,3,.2);
}
.toolbar-title{
    text-align:center;
    color:#738399;
    font-size:14px;
    font-family:"Microsoft Yahei";
}
.toolbar-split{
    float:left;
    width:1px;
    height:90px;
    background:#a5b7d0;
    box-shadow:0 0 0 1px #ecf1fa;
}
.color-lst{
    overflow:hidden;
}
.color-lst li{
    float:left;
    margin:0 10px 4px 0;
    padding:2px;
    background:#fff;
    border:1px solid #aabbd2;
}
.color-lst li span{
    display:block;
    width:22px;
    height:22px;
}
.color-lst .black span{
    background:#000;
}
.color-lst .red span{
    background:#f00;
}
.color-lst .blue span{
    background:#00f;
}
.color-lst .green span{
    background:#0f0;
}
.color-lst .yellow span{
    background:#ff0;
}
.color-lst .selected{
    border-color:#ffc762;
}
.toolbar-lineWidth-main{
    position:relative;
    width:100px;
    overflow:hidden;
}
.line-width-lst{

    width:100px;
}

.toolbar-lineWidth-main:hover{
    overflow:visible;
}

.toolbar-lineWidth-main:hover .line-width-lst{
    position:absolute;
    top:0;
    left:0;
    background:#fff;
}
.line-width-lst li{
    width:100px;
    height:20px;
    line-height:20px;
}
.line-width-lst li span{
    display:inline-block;
    vertical-align:middle;
    width:100px;
    background:#000;
}
.line-width-1 span{
    height:1px;
}
.line-width-2 span{
    height:2px;
}
.line-width-3 span{
    height:3px;
}
.line-width-4 span{
    height:4px;
}
.line-width-5 span{
    height:5px;
}
.line-width-lst .selected{
    background:#ffc762;
}
```

drawBoard.js：

``` javascript
function DrawBoard(){
    this.oCanvas=document.getElementById("canvas");
      this.oCxt=this.oCanvas.getContext("2d");
      this.iCvsLeft=this.oCanvas.offsetLeft;
      this.iCvsTop=this.oCanvas.offsetTop;
    this.init();
}

DrawBoard.prototype={
    constructor: DrawBoard,

    /**
     * 初始化各种方法
     * 
    **/
    init:function(){

          this.scroll();

          //设置默认的工具
          this.colorChoose();
          this.lineWidthChoose();
          this.shapeChoose();
          this.toolChoose();

          //初始化工具事件
          this.colorFn();
          this.lineWidthFn();
          this.shapeFn();
          this.toolFn();
    },

    /**
     * 滚动条滚动后重新计算鼠标绘制位置
     * 
    **/
    scroll:function(){
          var _this=this;
          document.onscroll=function(){
              var scrollTop=document.documentElement.scrollTop||document.body.scrollTop;
              var scrollLeft=document.documentElement.scrollLeft||document.body.scrollLeft;
              _this.iCvsLeft=_this.oCanvas.offsetLeft - scrollLeft;
              _this.iCvsTop=_this.oCanvas.offsetTop - scrollTop;
          }
      },

      /**
     * 工具选择事件
     * 
    **/
      toolFn:function(){
          var _this=this;
          var aTool=document.querySelector(".js-tool-lst").children;
          for(var i=0;i<aTool.length;i++){
            aTool[i].onclick=function(){
                _this.toolChoose(this.getAttribute("data-tool"));
            }
        }
      },

      /**
     * 选择工具判断
     * @param sTool
    **/
    toolChoose:function(sTool){
        if(sTool=="eraserAll"){
            this.clearRectAll();
        }else if(sTool=="eraser"){
            this.toolSwitch("pointer",sTool);
            this.clearRect();
        }
    },

    /**
     * 形状选择事件
     * 
    **/
    shapeFn:function(){
        var _this=this;
        var aShape=document.querySelector(".js-shape-lst").children;
        for(var i=0;i<aShape.length;i++){
            aShape[i].onclick=function(){
                for(var j=0;j<aShape.length;j++){
                    aShape[j].classList.remove("selected");
                }
                this.classList.add("selected");
                _this.shapeChoose(this.getAttribute("data-tool"));
            }
        }
    },

    /**
     * 形状选择判断
     * @param sShape
    **/
    shapeChoose:function(sShape){
        this.toolSwitch("crosshair",sShape);
        if(sShape=="rect"){
            this.drawRect();
        }else if(sShape=="arc"){
            this.drawArc();
        }else{
            this.drawLine();
        }
    },

    /**
     * 颜色选择事件
     * 
    **/
    colorFn:function(){
        var _this=this;
        var aColor=document.querySelector(".js-color-lst").children;
        for(var i=0;i<aColor.length;i++){
            aColor[i].onclick=function(){
                for(var j=0;j<aColor.length;j++){
                    aColor[j].classList.remove("selected");
                }
                this.classList.add("selected");
                _this.colorChoose(this.getAttribute("data-color"));
            }
        }
    },

    /**
     * 颜色选择判断
     * @param sColor
    **/
    colorChoose:function(sColor){
        this.sColor=sColor?sColor:"#000";
        this.oCxt.strokeStyle=this.sColor;
    },

    /**
     * 线条选择事件
     * 
    **/
    lineWidthFn:function(){
        var _this=this;
        var aLineWidth=document.querySelector(".js-line-width-lst").children;
        for(var i=0;i<aLineWidth.length;i++){
            aLineWidth[i].onclick=function(){
                for(var j=0;j<aLineWidth.length;j++){
                    aLineWidth[j].classList.remove("selected");
                }
                this.classList.add("selected");
                _this.lineWidthChoose(this.getAttribute("data-lineWidth"));
            }
        }
    },

    /**
     * 线条选择判断
     * @param iLineWidth
    **/
    lineWidthChoose:function(iLineWidth){
        this.iLineWidth=iLineWidth?iLineWidth:1;
        this.oCxt.lineWidth=this.iLineWidth;
    },

    /**
     * 创建虚拟canvas--绘制矩形和圆形时，鼠标按下创建，鼠标抬起移除
     * 
    **/
    virtualCanvasCreate:function(){
        var vCs=this.oCanvas.cloneNode(true);
        vCs.id="virtualCanvas";
        vCs.style.background="transparent";
        vCs.style.boxShadow="none";
        vCs.style.position="absolute";
        vCs.style.left=this.oCanvas.offsetLeft+"px";
        vCs.style.top=this.oCanvas.offsetTop+"px";
        var vCxt=vCs.getContext("2d");
        document.querySelector("body").appendChild(vCs);
        return {
            vCanvas:vCs,
            vCxt:vCxt
        }
    },

    /**
     * 移除虚拟canvas
     * 
    **/
    virtualCanvasRemove:function(vCs){
        document.querySelector("body").removeChild(vCs);
    },

    /**
     * 绘制线条
     * 
    **/
      drawLine:function(){
          var _this=this;
          this.oCanvas.onmousedown=function(event){
            var oEvent=event||window.event;
            var startX=oEvent.clientX-_this.iCvsLeft;
            var startY=oEvent.clientY-_this.iCvsTop;
            var endX=startX;
            var endY=startY;

            _this.oCxt.beginPath();
            _this.oCxt.moveTo(startX,startY);

            document.onmousemove=function(event){
              var oEvent=event||window.event;
              endX=oEvent.clientX-_this.iCvsLeft;
              endY=oEvent.clientY-_this.iCvsTop;

              _this.oCxt.lineTo(endX,endY);
              _this.oCxt.stroke();

              return false;
            }
            document.onmouseup=function(){
              document.onmousemove=null;
              document.onmouseup=null;
            }
        }
      },

      /**
     * 绘制矩形
     * 
    **/
      drawRect:function(){
          var _this=this;
          this.oCanvas.onmousedown=function(event){
              var oEvent=event||window.event;
              var startX=oEvent.clientX-_this.iCvsLeft;
            var startY=oEvent.clientY-_this.iCvsTop;
            var endX=startX;
            var endY=startY;

            var virtualCanvas=_this.virtualCanvasCreate();
              virtualCanvas.vCxt.strokeStyle=_this.sColor;
              virtualCanvas.vCxt.lineWidth=_this.iLineWidth;
              var delta=0;
            if(_this.iLineWidth%2==1){
                delta=.5;
            }

            document.onmousemove=function(event){
              var oEvent=event||window.event;
              endX=oEvent.clientX-_this.iCvsLeft;
              endY=oEvent.clientY-_this.iCvsTop;
              
              virtualCanvas.vCxt.clearRect(0,0,virtualCanvas.vCanvas.width,virtualCanvas.vCanvas.height);  //清除虚拟canvas
              virtualCanvas.vCxt.strokeRect(startX+delta,startY+delta,endX-startX,endY-startY);   //在虚拟canvas上绘制

              return false;
            }
            document.onmouseup=function(){
              if(endX-startX!=0&&endY-startY!=0){
                  _this.oCxt.strokeRect(startX+delta,startY+delta,endX-startX,endY-startY);  //鼠标松开在真实canvas上绘制
              }
              _this.virtualCanvasRemove(virtualCanvas.vCanvas);

              document.onmousemove=null;
              document.onmouseup=null;
            }
          }
      },

      /**
     * 绘制圆形
     * 
    **/
      drawArc:function(){
          var _this=this;
          this.oCanvas.onmousedown=function(event){
              var oEvent=event||window.event;
              var startX=oEvent.clientX-_this.iCvsLeft;
            var startY=oEvent.clientY-_this.iCvsTop;
            var endX=startX;
            var endY=startY;
            var disX,disY,radius,coordsX,coordsY;

            var virtualCanvas=_this.virtualCanvasCreate();
              virtualCanvas.vCxt.strokeStyle=_this.sColor;
              virtualCanvas.vCxt.lineWidth=_this.iLineWidth;
              
            document.onmousemove=function(event){
              var oEvent=event||window.event;
              endX=oEvent.clientX-_this.iCvsLeft;
              endY=oEvent.clientY-_this.iCvsTop;
              disX=(endX-startX)/2;
              disY=(endY-startY)/2;
              radius=Math.min(Math.abs(disX),Math.abs(disY));
              coordsX=disX < 0 ? -radius : radius;
              coordsY=disY < 0 ? -radius : radius;
              
              virtualCanvas.vCxt.clearRect(0,0,virtualCanvas.vCanvas.width,virtualCanvas.vCanvas.height);  //清除虚拟canvas
              virtualCanvas.vCxt.beginPath();
              virtualCanvas.vCxt.arc(startX+coordsX,startY+coordsY,radius,0,2*Math.PI,false);   //在虚拟canvas上绘制
              virtualCanvas.vCxt.stroke();

              return false;
            }
            document.onmouseup=function(){
              if(endX-startX!=0&&endY-startY!=0){
                  _this.oCxt.beginPath();
                  _this.oCxt.arc(startX+coordsX,startY+coordsY,radius,0,2*Math.PI,false); //鼠标松开在真实canvas上绘制
                  _this.oCxt.stroke();
              }
              _this.virtualCanvasRemove(virtualCanvas.vCanvas);

              document.onmousemove=null;
              document.onmouseup=null;
            }
          }
      },

      /**
     * 工具切换后，鼠标样式的切换
     * 
    **/
      toolSwitch:function(sCursorStyle,sTool){
          this.oCanvas.style="cursor:"+sCursorStyle;
      },

      /**
     * 清楚画布
     * 
    **/
      clearRectAll:function(){
          this.oCxt.clearRect(0,0,this.oCanvas.width,this.oCanvas.height);
      }
}
```