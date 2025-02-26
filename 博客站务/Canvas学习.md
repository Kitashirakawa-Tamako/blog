# 初识Canvas

##  基础概念

1. HTML5 `<canvas>` 标签用于绘制图像；
2. `<canvas>`元素本身没有绘制能力（它仅仅作为图形的容器，称为画布），必须使用脚本（通常是JavaScript，称为画笔）完成绘制任务；
3. Internet Explorer 8 及更早的IE版本不支持 `<canvas>` 元素。

##  内置属性

以下为`getContext("2d")`对象的属性和方法，可用于在画布上绘制文本、线条、矩形、圆形等等。

### 1. 颜色、样式和阴影

| 属性                                                         | 描述                                       |
| ------------------------------------------------------------ | ------------------------------------------ |
| [fillStyle](https://www.runoob.com/tags/canvas-fillstyle.html) | 设置或返回用于填充绘画的颜色、渐变或模式。 |
| [strokeStyle](https://www.runoob.com/tags/canvas-strokestyle.html) | 设置或返回用于笔触的颜色、渐变或模式。     |
| [shadowColor](https://www.runoob.com/tags/canvas-shadowcolor.html) | 设置或返回用于阴影的颜色。                 |
| [shadowBlur](https://www.runoob.com/tags/canvas-shadowblur.html) | 设置或返回用于阴影的模糊级别。             |
| [shadowOffsetX](https://www.runoob.com/tags/canvas-shadowoffsetx.html) | 设置或返回阴影与形状的水平距离。           |
| [shadowOffsetY](https://www.runoob.com/tags/canvas-shadowoffsety.html) | 设置或返回阴影与形状的垂直距离。           |

| 方法                                                         | 描述                                      |
| ------------------------------------------------------------ | ----------------------------------------- |
| [createLinearGradient()](https://www.runoob.com/tags/canvas-createlineargradient.html) | 创建线性渐变（用在画布内容上）。          |
| [createPattern()](https://www.runoob.com/tags/canvas-createpattern.html) | 在指定的方向上重复指定的元素。            |
| [createRadialGradient()](https://www.runoob.com/tags/canvas-createradialgradient.html) | 创建放射状/环形的渐变（用在画布内容上）。 |
| [addColorStop()](https://www.runoob.com/tags/canvas-addcolorstop.html) | 规定渐变对象中的颜色和停止位置。          |

### 2. 线条样式

| 方法                                                         | 描述                                       |
| ------------------------------------------------------------ | ------------------------------------------ |
| [lineCap](https://www.runoob.com/tags/canvas-linecap.html)   | 设置或返回线条的结束端点样式。             |
| [lineJoin](https://www.runoob.com/tags/canvas-linejoin.html) | 设置或返回两条线相交时，所创建的拐角类型。 |
| [lineWidth](https://www.runoob.com/tags/canvas-linewidth.html) | 设置或返回当前的线条宽度。                 |
| [miterLimit](https://www.runoob.com/tags/canvas-miterlimit.html) | 设置或返回最大斜接长度。                   |

### 3. 矩形

| 方法                                                         | 描述                           |
| ------------------------------------------------------------ | ------------------------------ |
| [rect()](https://www.runoob.com/tags/canvas-rect.html)       | 创建矩形。                     |
| [fillRect()](https://www.runoob.com/tags/canvas-fillrect.html) | 绘制"被填充"的矩形。           |
| [strokeRect()](https://www.runoob.com/tags/canvas-strokerect.html) | 绘制矩形（无填充）。           |
| [clearRect()](https://www.runoob.com/tags/canvas-clearrect.html) | 在给定的矩形内清除指定的像素。 |

### 4. 路径

| 方法                                                         | 描述                                                      |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| [fill()](https://www.runoob.com/tags/canvas-fill.html)       | 填充当前绘图（路径）。                                    |
| [stroke()](https://www.runoob.com/tags/canvas-stroke.html)   | 绘制已定义的路径。                                        |
| [beginPath()](https://www.runoob.com/tags/canvas-beginpath.html) | 起始一条路径，或重置当前路径。                            |
| [moveTo()](https://www.runoob.com/tags/canvas-moveto.html)   | 把路径移动到画布中的指定点，不创建线条。                  |
| [closePath()](https://www.runoob.com/tags/canvas-closepath.html) | 创建从当前点回到起始点的路径。                            |
| [lineTo()](https://www.runoob.com/tags/canvas-lineto.html)   | 添加一个新点，然后在画布中创建从该点到最后指定点的线条。  |
| [clip()](https://www.runoob.com/tags/canvas-clip.html)       | 从原始画布剪切任意形状和尺寸的区域。                      |
| [quadraticCurveTo()](https://www.runoob.com/tags/canvas-quadraticcurveto.html) | 创建二次贝塞尔曲线。                                      |
| [bezierCurveTo()](https://www.runoob.com/tags/canvas-beziercurveto.html) | 创建三次贝塞尔曲线。                                      |
| [arc()](https://www.runoob.com/tags/canvas-arc.html)         | 创建弧/曲线（用于创建圆形或部分圆）。                     |
| [arcTo()](https://www.runoob.com/tags/canvas-arcto.html)     | 创建两切线之间的弧/曲线。                                 |
| [isPointInPath()](https://www.runoob.com/tags/canvas-ispointinpath.html) | 如果指定的点位于当前路径中，则返回 true，否则返回 false。 |

### 5. 转换

| 方法                                                         | 描述                                             |
| ------------------------------------------------------------ | ------------------------------------------------ |
| [scale()](https://www.runoob.com/tags/canvas-scale.html)     | 缩放当前绘图至更大或更小。                       |
| [rotate()](https://www.runoob.com/tags/canvas-rotate.html)   | 旋转当前绘图。                                   |
| [translate()](https://www.runoob.com/tags/canvas-translate.html) | 重新映射画布上的 (0,0) 位置。                    |
| [transform()](https://www.runoob.com/tags/canvas-transform.html) | 替换绘图的当前转换矩阵。                         |
| [setTransform()](https://www.runoob.com/tags/canvas-settransform.html) | 将当前转换重置为单位矩阵。然后运行 transform()。 |

### 6. 文本

| 属性                                                         | 描述                                       |
| ------------------------------------------------------------ | ------------------------------------------ |
| [font](https://www.runoob.com/tags/canvas-font.html)         | 设置或返回文本内容的当前字体属性。         |
| [textAlign](https://www.runoob.com/tags/canvas-textalign.html) | 设置或返回文本内容的当前对齐方式。         |
| [textBaseline](https://www.runoob.com/tags/canvas-textbaseline.html) | 设置或返回在绘制文本时使用的当前文本基线。 |

| 方法                                                         | 描述                         |
| ------------------------------------------------------------ | ---------------------------- |
| [fillText()](https://www.runoob.com/tags/canvas-filltext.html) | 在画布上绘制"被填充的"文本。 |
| [strokeText()](https://www.runoob.com/tags/canvas-stroketext.html) | 在画布上绘制文本（无填充）。 |
| [measureText()](https://www.runoob.com/tags/canvas-measuretext.html) | 返回包含指定文本宽度的对象。 |

### 7. 图像绘制

| 方法                                                         | 描述                           |
| ------------------------------------------------------------ | ------------------------------ |
| [drawImage()](https://www.runoob.com/tags/canvas-drawimage.html) | 向画布上绘制图像、画布或视频。 |

### 8. 像素操作

| 属性                                                         | 描述                                                  |
| ------------------------------------------------------------ | ----------------------------------------------------- |
| [width](https://www.runoob.com/tags/canvas-imagedata-width.html) | 返回 ImageData 对象的宽度。                           |
| [height](https://www.runoob.com/tags/canvas-imagedata-height.html) | 返回 ImageData 对象的高度。                           |
| [data](https://www.runoob.com/tags/canvas-imagedata-data.html) | 返回一个对象，其包含指定的 ImageData 对象的图像数据。 |

| 方法                                                         | 描述                                                        |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| [createImageData()](https://www.runoob.com/tags/canvas-createimagedata.html) | 创建新的、空白的 ImageData 对象。                           |
| [getImageData()](https://www.runoob.com/tags/canvas-getimagedata.html) | 返回 ImageData 对象，该对象为画布上指定的矩形复制像素数据。 |
| [putImageData()](https://www.runoob.com/tags/canvas-putimagedata.html) | 把图像数据（从指定的 ImageData 对象）放回画布上。           |

### 9. 合成

| 属性                                                         | 描述                                     |
| ------------------------------------------------------------ | ---------------------------------------- |
| [globalAlpha](https://www.runoob.com/tags/canvas-globalalpha.html) | 设置或返回绘图的当前 alpha 或透明值。    |
| [globalCompositeOperation](https://www.runoob.com/tags/canvas-globalcompositeoperation.html) | 设置或返回新图像如何绘制到已有的图像上。 |

### 10. 其他

| 方法          | 描述                             |
| ------------- | -------------------------------- |
| save()        | 保存当前环境的状态。             |
| restore()     | 返回之前保存过的路径状态和属性。 |
| createEvent() |                                  |
| getContext()  |                                  |
| toDataURL()   |                                  |

##  示例

### 1. 完整代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Canvas</title>
</head>
<body>
    <!--定义一个画布包含基本3个属性-->
    <canvas id="demo" width="500" height="500">
        <!-- 此处的文字正常不会显示-->
        您的浏览器不支持canvas
    </canvas>
</body>
<script>
    //1找到画布对象
    var canvas = document.getElementById('demo')
    console.log([canvas])
    //2获取上下文对象（画笔）
    var ctx = canvas.getContext('2d')
    console.log(ctx)
    //3绘制路径
    ctx.rect(50,50,300,300)
    //4填充
    ctx.fillStyle = 'aqua'
    ctx.fill()
    //5描边，渲染路径
    ctx.lineWidth = 20
    ctx.strokeStyle = 'salmon'
    ctx.stroke()
</script>
</html>
```

### 2. 运行截图

![215056.png](/image/54d31c62-af69-429d-8aca-4747b18fbad6.png)
# 绘制图形和文本

##  绘制路径

两点可以确定一条直线，把两点用直线连接起来就得到一条线段，多条连续的线段可以组成路径。
这其中有一个[miterLimit](https://www.canvasapi.cn/CanvasRenderingContext2D/miterLimit)属性，比较复杂难懂。

```html
<body>
    <canvas id="canvas1" width="400" height="400"></canvas>
</body>
<script>
    var canvas1 = document.getElementById('canvas1')
    var ctx1 = canvas1.getContext('2d')

    //设置开始路径
    ctx1.beginPath()
    //设置绘制的起始点
    ctx1.moveTo(50, 50)
    //设置经过某个位置
    ctx1.lineTo(50, 300)
    //设置经过某个位置
    ctx1.lineTo(300, 100)
    //设置经过某个位置
    ctx1.lineTo(300, 250)
    //设置结束路径，从当前点回到起始点
    ctx1.closePath()

    //绘制路径
    ctx1.lineCap = 'round' //起始路径的线段边缘设置为圆角，起点、终点
    // ctx.lineJoin = 'round' //拐角的样式
    ctx1.miterLimit = 1 //设置最大斜接长度，参数为1、2、3
    ctx1.strokeStyle = 'aqua'
    ctx1.lineWidth = 40
    ctx1.stroke()
</script>
```

##  画一个圆（圆弧）

通过确定圆心坐标、半径、圆周大小、时针方向可以得到一个圆形或圆弧。

```html
<body>
	<canvas id="canvas2" width="400" height="400"></canvas>
</body>
<script>
    var canvas2 = document.getElementById('canvas2')
    var ctx2 = canvas2.getContext('2d')
    //坐标x、坐标y、半径、起始角度、结束角度、方向（true代表逆时针）
    ctx2.arc(200, 300, 100, 0, Math.PI, true)
    //填充颜色
    ctx2.fillStyle = 'bisque'
    ctx2.fill()
    ctx2.stroke()
</script>
```

##  文字

```html
<body>
	<canvas id="canvas3" width="400" height="400"></canvas>
</body>
<script>
    var canvas3 = document.getElementById('canvas3')
    var ctx3 = canvas3.getContext('2d')
    ctx3.font = '50px 微软雅黑'
    //设置阴影
    ctx3.shadowBlur = 20
    ctx3.shadowColor = 'rgb(0, 0, 0)'
    ctx3.shadowOffsetX = 10
    ctx3.shadowOffsetY = 10
    var x = 500
    //一般写法，实现一个弹幕,10ms刷新一次
    setInterval(() => {
        //清空画布
        ctx3.clearRect(0, 0, 400, 400)
        x -= 1
        if (x < -100) {
            x = 500
        }
        ctx3.fillText('helloworld', x, 100)
        ctx3.strokeText('中午吃啥', x, 200)
    }, 10)

</script>
```

因为屏幕刷新时间与动画帧数不一致，所以看上去感觉动画不是很流畅，可以使用`window.requestAnimationFrame`优化。

##  完整代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<canvas id="canvas1" width="400" height="400"></canvas>
<canvas id="canvas2" width="400" height="400"></canvas>
<canvas id="canvas3" width="400" height="400"></canvas>
<canvas id="canvas4" width="400" height="400"></canvas>
</body>
<script>
    var canvas1 = document.getElementById('canvas1')
    var ctx1 = canvas1.getContext('2d')

    //设置开始路径
    ctx1.beginPath()
    //设置绘制的起始点
    ctx1.moveTo(50, 50)
    //设置经过某个位置
    ctx1.lineTo(50, 300)
    //设置经过某个位置
    ctx1.lineTo(300, 100)
    //设置经过某个位置
    ctx1.lineTo(300, 250)
    //设置结束路径，从当前点回到起始点
    ctx1.closePath()

    //绘制路径
    ctx1.lineCap = 'round' //起始路径的线段边缘设置为圆角，起点、终点
    // ctx.lineJoin = 'round' //拐角的样式
    ctx1.miterLimit = 1 //设置最大斜接长度，参数为1、2、3
    ctx1.strokeStyle = 'aqua'
    ctx1.lineWidth = 20
    ctx1.stroke()
</script>
<script>
    var canvas2 = document.getElementById('canvas2')
    var ctx2 = canvas2.getContext('2d')
    //坐标x、坐标y、半径、起始角度、结束角度、方向（true代表逆时针）
    ctx2.arc(200, 300, 100, 0, Math.PI, true)
    //填充颜色
    ctx2.fillStyle = 'bisque'
    ctx2.fill()
    ctx2.stroke()
</script>
<script>
    var canvas3 = document.getElementById('canvas3')
    var ctx3 = canvas3.getContext('2d')
    ctx3.font = '50px 微软雅黑'
    //设置阴影
    ctx3.shadowBlur = 20
    ctx3.shadowColor = 'rgb(0, 0, 0)'
    ctx3.shadowOffsetX = 10
    ctx3.shadowOffsetY = 10
    var x = 500
    //一般写法，实现一个弹幕,10ms刷新一次,会有抖动感
    setInterval(() => {
        //清空画布
        ctx3.clearRect(0, 0, 400, 400)
        x -= 1
        if (x < -100) {
            x = 500
        }
        ctx3.fillText('helloworld', x, 100)
        ctx3.strokeText('中午吃啥', x, 200)
    }, 10)

</script>
<script>
    var canvas4 = document.getElementById('canvas4')
    var ctx4 = canvas4.getContext('2d')
    ctx4.font = '50px 微软雅黑'
    //设置阴影
    ctx4.shadowBlur = 20
    ctx4.shadowColor = 'rgb(0, 0, 0)'
    ctx4.shadowOffsetX = 10
    ctx4.shadowOffsetY = 10
    var y = 500
    //动画帧数与屏幕刷新率相同
    var animation = () => {
        ctx4.clearRect(0, 0, 400, 400)
        y -= 1
        if (y < -100) {
            y = 500
        }
        ctx4.fillText('helloworld', y, 100)
        ctx4.strokeText('中午吃啥', y, 200)
        window.requestAnimationFrame(animation)
    }
    window.requestAnimationFrame(animation)
</script>
</html>
```

# 绘制图像

根据图片绘制

使用[drawImage()](https://www.runoob.com/tags/canvas-drawimage.html)方法，可以把一个已有的媒体资源绘制到画布上。

```html
<canvas id="canvas1" width="600" height="600"></canvas>
<script>
    var canvas1 = document.getElementById('canvas1')
    var ctx1 = canvas1.getContext('2d')
    //绘制图像
    //ctx.drawImage(图像对象,x坐标,y坐标)
    //ctx.drawImage(图像对象,x坐标,y坐标,宽度,高度)
    //ctx.drawImage(图像对象,图像裁剪位置x,图片裁剪位置y,裁剪宽度,裁剪高度,x坐标,y坐标,宽度,高度)
    var img = new Image()
    img.src = '1.jpg'
    img.onload = () => {
        //这里应该是先裁剪，得到裁剪的新图片（新的内容和新尺寸），再进行位置和大小的设置
        ctx1.drawImage(img, 50, 100, 500, 500, 50, 50, 300, 400)
    }
</script>
```

一定要在`img.onload`中执行绘制操作，因为浏览器加载图像时需要时间的（加载图像到内存中），而代码的执行时几乎不需要时间的，所以必须等待浏览器加载图片后（自动调用方法），在回调函数中进行绘制。

裁剪并绘制时，先根据原图像裁剪得到一张新的图像，在根据坐标和像素绘制图片，会完全绘制新图像并改变图像

的比例。

例：从一个800x800的图片中随意扣出400x400的图片后，如果绘制为400x200的图像，那图片就会被“压扁”。

##  根据视频绘制

```html
<canvas id="canvas2" width="600" height="600"></canvas>
<video id="video" width="800" height="" src="1.mp4" controls="controls"></video>
<script>
    var video = document.getElementById('video')
    var canvas2 = document.getElementById('canvas2')
    var ctx2 = canvas2.getContext('2d')
    var interId
    video.onplay = () => {
        interId = setInterval(() => {
            ctx2.clearRect(0, 0, 600, 600)
            ctx2.fillRect(0, 0, 600, 600)
            ctx2.drawImage(video, 0, 0, 600, 500)
            ctx2.font = '20px 微软雅黑'
            ctx2.strokeStyle = '#999'
            ctx2.strokeText('姜皓育', 100, 20)
        }, 16)
    }
    video.onpause = () => {
        clearInterval(interId)
    }
</script>
```

drawImage()会自动截取视频当前帧的图像，所以每16ms（约等于60帧）重复执行一次，就可以绘制一个视频，并为视频加上水印。

##  完整代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<canvas id="canvas1" width="600" height="600"></canvas>
<br>
<canvas id="canvas2" width="600" height="600"></canvas>
<video id="video" width="800" height="" src="1.mp4" controls="controls"></video>
</body>
<script>
    var canvas1 = document.getElementById('canvas1')
    var ctx1 = canvas1.getContext('2d')
    //绘制图像
    //ctx.drawImage(图像对象,x坐标,y坐标)
    //ctx.drawImage(图像对象,x坐标,y坐标,宽度,高度)
    //ctx.drawImage(图像对象,图像裁剪位置x,图片裁剪位置y,裁剪宽度,裁剪高度,x坐标,y坐标,宽度,高度)
    var img = new Image()
    img.src = '1.jpg'
    img.onload = () => {
        //这里应该是先裁剪，得到裁剪的新图片（新的内容和新尺寸），再进行位置和大小的设置
        ctx1.drawImage(img, 50, 100, 500, 500, 50, 50, 300, 400)
    }
</script>
<script>
    var video = document.getElementById('video')
    var canvas2 = document.getElementById('canvas2')
    var ctx2 = canvas2.getContext('2d')
    var interId
    video.onplay = () => {
        interId = setInterval(() => {
            ctx2.clearRect(0, 0, 600, 600)
            ctx2.fillRect(0, 0, 600, 600)
            ctx2.drawImage(video, 0, 0, 600, 500)
            ctx2.font = '20px 微软雅黑'
            ctx2.strokeStyle = '#999'
            ctx2.strokeText('姜皓育', 100, 20)
        }, 16)
    }
    video.onpause = () => {
        clearInterval(interId)
    }
</script>
</html>
```

# 绘制一个钟表

##  坐标系

html中默认以元素坐上定点为坐标轴原点（0,0），横向为x轴向右为正，纵向为y轴向下为正，单位为1像素。

那么，我们可以通过平移、旋转、缩放得到一个新的坐标系。

此处请利用数学知识理解。

```html
<canvas id="canvas1" width="800" height="800"></canvas>
```

上面的例子中，分别在不同的坐标系中绘制了一个矩形元素。

##  绘制钟表

### 1. 效果

首先给出最终展示效果：
![145749.gif](/image/7374bd25-6d18-4708-8ee1-d0b4b440be79.gif)

### 2. 基础思想

先看一下钟表的基本结构，从百度上找一个普通的钟表图片，观察其中的结构和内容。

![112659.jpg](/image/817eb582-e69b-4f73-b284-bbcaf1071f03.jpg)

首先要明确的一下几个事情：

1. 钟表每隔一秒钟发生一次变化，所以我们每间隔一秒钟绘制一次图像；
2. 每秒绘图时，都要清空画布，重新绘制。
3. 每次绘制前要先保存（save）当前画笔定义，绘制后要还原（restore）画笔状态，每个save()只能被restore()一次。

定义一个render函数，用于渲染画布，通过定时器调用，基本结构为：

```html
<canvas id="canvas2" width="800" height="600"></canvas>
<script>
    var canvas2 = document.getElementById('canvas2')
    var cxt2 = canvas2.getContext('2d')
    var render = () => {
        //首先清空画布
        cxt2.clearRect(0,0,800,600)
        //保存格式化状态的画笔
        cxt2.save()
        //以下是渲染逻辑，下文的代码都在此处运行
    }
    setInterval(()=>{
        render()
    },1000)
</script>
```

### 3. 调整坐标轴

画布初始化为800x600，为了更符合数学思想，我们把画布中心坐标（400,300）作为原点，并且因为以12点方向为x轴（因为钟表以12点方向作为起始角度），简单画图表示：

![135220.png](/image/9c6fa74e-c7e0-4128-acaf-0c9cb8f96c86.png)
```javascript
//将坐标移动到画布的中央
cxt2.translate(400, 300)
//大于0为顺时针，反之为逆时针，2π==360°
cxt2.rotate(-2 * Math.PI / 4)
//保存坐标轴状态的画笔
cxt2.save()
```

### 4. 绘制表盘

1. 绘制表盘圆形状：

   以画布中心（坐标轴原点）为圆心，绘制半径为200像素的圆，线条宽度为10像素。

   ```javascript
   //绘制表盘
   cxt2.beginPath()
   //表圆形
   cxt2.arc(0, 0, 200, 0, 2 * Math.PI)
   cxt2.strokeStyle = 'darkgrey'
   cxt2.lineWidth = 10
   cxt2.stroke()
   cxt2.closePath()
   ```

2. 绘制秒/分钟刻度：

   因为12点方向为x轴，所以先绘制12点方向上的秒刻度（实际上12点方向是小时刻度），秒刻度样式设为深灰色、长度15，宽度2，从x=185作为起始点沿着x轴绘制。

   绘制完一个点之后顺时针旋转坐标轴6°，再绘制下一个刻度，共60次。

   ```javascript
   //绘制分钟刻度
   for (var j = 0; j < 60; j++) {
       cxt2.rotate(Math.PI / 30)
       cxt2.beginPath()
       cxt2.moveTo(185, 0)
       cxt2.lineTo(200, 0)
       cxt2.lineWidth = 2
       cxt2.strokeStyle = 'darkgrey'
       cxt2.stroke()
       cxt2.closePath()
   }
   //因为修改了画笔，要还原画笔状态
   cxt2.restore()
   //保存上一个画笔状态，即坐标轴状态
   cxt2.save()
   ```

3. 绘制小时刻度：

   同样的道理可以绘制出12个略长略粗的小时刻度，并覆盖原位置的秒刻度。

   ```javascript
   //绘制时钟刻度
   for (var i = 0; i < 12; i++) {
       cxt2.rotate(Math.PI / 6)
       cxt2.beginPath()
       cxt2.moveTo(180, 0)
       cxt2.lineTo(200, 0)
       cxt2.lineWidth = 5
       cxt2.strokeStyle = 'darkgrey'
       cxt2.stroke()
       cxt2.closePath()
   }
   ```

### 5. 绘制表针

1. 获取当前系统时间

   ```javascript
   var time = new Date()
   var hour = time.getHours()
   var min = time.getMinutes()
   var sec = time.getSeconds()
   console.log(time)
   //如果小时大于12，就减去12
   hour = hour > 12 ? hour - 12 : hour
   ```

2. 绘制秒针，红色最细最长

   ```javascript
   //绘制秒针
   cxt2.beginPath()
   cxt2.rotate(2 * Math.PI / 60 * sec)
   cxt2.moveTo(-30, 0)
   cxt2.lineTo(170, 0)
   cxt2.lineWidth = 2
   cxt2.strokeStyle = 'red'
   cxt2.stroke()
   cxt2.closePath()
   cxt2.restore()
   cxt2.save()
   ```

3. 绘制分针，分针需要考虑不足整分钟时的偏移量

   ```javascript
   //绘制分针
   cxt2.beginPath()
   cxt2.rotate(2 * Math.PI / 60 * min + 2 * Math.PI / 3600 * sec)
   cxt2.moveTo(-20, 0)
   cxt2.lineTo(150, 0)
   cxt2.lineWidth = 5
   cxt2.strokeStyle = 'darkblue'
   cxt2.stroke()
   cxt2.closePath()
   cxt2.restore()
   cxt2.save()
   ```

4. 绘制时针，可以不考虑秒针带来的偏移

   ```javascript
   //绘制时针
   cxt2.beginPath()
   cxt2.rotate(2 * Math.PI / 12 * hour + 2 * Math.PI / 60 / 12 * min + 2 * Math.PI / 12 / 60 / 60 * sec)
   cxt2.moveTo(-10, 0)
   cxt2.lineTo(130, 0)
   cxt2.lineWidth = 8
   cxt2.strokeStyle = 'darkslategray'
   cxt2.stroke()
   cxt2.closePath()
   cxt2.restore()
   cxt2.save()
   ```

5. 三根表针交叉处有一个圆，设置为10半径蓝色

   ```javascript
   //交叉处样式
   cxt2.beginPath()
   cxt2.arc(0,0,10,0,2*Math.PI)
   cxt2.fillStyle = 'deepskyblue'
   cxt2.fill()
   cxt2.closePath()
   cxt2.restore()
   ```

6. 还原画笔状态

   当前最后一次的画笔状态为坐标轴状态，我们要将其还原至初始化状态，也就是第一次save()的状态

   ```javascript
   cxt2.restore()
   ```

##  完整代码

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<canvas id="canvas1" width="800" height="800"></canvas>
<canvas id="canvas2" width="800" height="600"></canvas>
</body>
<script>
    var canvas1 = document.getElementById('canvas1')
    var cxt1 = canvas1.getContext('2d')
    //保留环境的状态（画笔的状态）
    cxt1.save()
    //移动坐标原点，默认为左上角（0,0）
    cxt1.translate(400, 400)
    //顺时针旋转坐标轴，弧度制
    cxt1.rotate(Math.PI / 4)
    //放大坐标轴，不会修改已经画完的
    cxt1.scale(2, 2)
    cxt1.fillStyle = 'deepskyblue'
    cxt1.fillRect(0, 50, 100, 100)
    //恢复之前保留的画笔状态（多次保留可以多次恢复）
    cxt1.restore()
    cxt1.fillStyle = 'hotpink'
    cxt1.fillRect(0, 0, 100, 100)
</script>
<script>
    var canvas2 = document.getElementById('canvas2')
    var cxt2 = canvas2.getContext('2d')
    var render = () =>{
        //首先清空画布
        cxt2.clearRect(0,0,800,600)
        //保存格式化状态的画笔
        cxt2.save()
        //将坐标移动到画布的中央
        cxt2.translate(400, 300)
        //大于0为顺时针，反之为逆时针，2π==360°
        cxt2.rotate(-2 * Math.PI / 4)
        //保存坐标轴状态的画笔
        cxt2.save()
        //绘制表盘
        cxt2.beginPath()
        //表圆形
        cxt2.arc(0, 0, 200, 0, 2 * Math.PI)
        cxt2.strokeStyle = 'darkgrey'
        cxt2.lineWidth = 10
        cxt2.stroke()
        cxt2.closePath()

        //绘制分钟刻度
        for (var j = 0; j < 60; j++) {
            cxt2.rotate(Math.PI / 30)
            cxt2.beginPath()
            cxt2.moveTo(185, 0)
            cxt2.lineTo(200, 0)
            cxt2.lineWidth = 2
            cxt2.strokeStyle = 'darkgrey'
            cxt2.stroke()
            cxt2.closePath()
        }
        //因为修改了画笔，要还原画笔状态
        cxt2.restore()
        //保存上一个画笔状态，即坐标轴状态
        cxt2.save()
        //绘制时钟刻度
        for (var i = 0; i < 12; i++) {
            cxt2.rotate(Math.PI / 6)
            cxt2.beginPath()
            cxt2.moveTo(180, 0)
            cxt2.lineTo(200, 0)
            cxt2.lineWidth = 5
            cxt2.strokeStyle = 'darkgrey'
            cxt2.stroke()
            cxt2.closePath()
        }
        cxt2.restore()
        cxt2.save()
        var time = new Date()
        var hour = time.getHours()
        var min = time.getMinutes()
        var sec = time.getSeconds()
        console.log(time)
        //如果小时大于12，就减去12
        hour = hour > 12 ? hour - 12 : hour
        //绘制秒针
        cxt2.beginPath()
        cxt2.rotate(2 * Math.PI / 60 * sec)
        cxt2.moveTo(-30, 0)
        cxt2.lineTo(170, 0)
        cxt2.lineWidth = 2
        cxt2.strokeStyle = 'red'
        cxt2.stroke()
        cxt2.closePath()
        cxt2.restore()
        cxt2.save()
        //绘制分针
        cxt2.beginPath()
        cxt2.rotate(2 * Math.PI / 60 * min + 2 * Math.PI / 3600 * sec)
        cxt2.moveTo(-20, 0)
        cxt2.lineTo(150, 0)
        cxt2.lineWidth = 4
        cxt2.strokeStyle = 'darkblue'
        cxt2.stroke()
        cxt2.closePath()
        cxt2.restore()
        cxt2.save()
        //绘制时针
        cxt2.beginPath()
        cxt2.rotate(2 * Math.PI / 12 * hour + 2 * Math.PI / 60 / 12 * min + 2 * Math.PI / 12 / 60 / 60 * sec)
        cxt2.moveTo(-10, 0)
        cxt2.lineTo(130, 0)
        cxt2.lineWidth = 6
        cxt2.strokeStyle = 'darkslategray'
        cxt2.stroke()
        cxt2.closePath()
        cxt2.restore()
        cxt2.save()
        //交叉处样式
        cxt2.beginPath()
        cxt2.arc(0,0,10,0,2*Math.PI)
        cxt2.fillStyle = 'deepskyblue'
        cxt2.fill()
        cxt2.closePath()
        cxt2.restore()
        cxt2.restore()
    }
    setInterval(()=>{
        render()
    },1000)
</script>
</html>
```



