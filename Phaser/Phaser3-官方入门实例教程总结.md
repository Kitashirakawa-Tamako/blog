本文仅对Phaser相关api进行总结，具体实现请查看[官方教程](http://phaser.io/tutorials/making-your-first-phaser-3-game-chinese/index)，[点击这里](/phaser3/tutorial/index.html)去体验最终结果。
# Phaser基本配置
```javascript
var config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    scene: {
        preload: preload,
        create: create,
        update: update
    }
};
var game = new Phaser.Game(config);
function preload (){}
function create (){}
function update (){}
```
属性`type`可以是`Phaser.CANVAS`，或者`Phaser.WEBGL`，或者`Phaser.AUTO`。这是你要给你的游戏使用的渲染环境（context）。推荐值是`Phaser.AUTO`，它将自动尝试使用WebGL，如果浏览器或设备不支持，它将回退为Canvas。Phaser生成的画布元素（canvas element）将径直添加到文档中调用脚本的那个节点上，不过也可以在游戏配置中指定一个父容器，如果你需要的话。
> 在html文件中，画布元素将被添加到<body></body>中

属性width和height设定了Phaser即将生成的画布元素的尺寸，在此例中是800 x 600 像素。这是游戏显示所用的分辨率，而你的游戏世界（world）可以是任意尺寸。

> 画布尺寸仅代表在浏览器中展示的大小，不代表游戏世界中的大小。
# 加载资源

```javascript
function preload (){
    this.load.image('sky', 'assets/sky.png');
    this.load.image('ground', 'assets/platform.png');
    this.load.image('star', 'assets/star.png');
    this.load.image('bomb', 'assets/bomb.png');
    this.load.spritesheet('dude', 
        'assets/dude.png',
        { frameWidth: 32, frameHeight: 48 }
    );
}
```
这样将加载5个资源：4张图（image）和一个精灵表单（sprite sheet）。第一个参数，它叫资源的key（键值，即'sky'，'bomb'）。这个字符串是一个链接，指向已加载的资源，可以随意使用任何有效的JavaScript字符串作为键值。

`spritesheet`精灵图是一个整合了很多个小背景图标的大背景图。作用是为了有效减少服务器接收和发送请求的次数，提高页面的加载速度。Phaser能根据精灵尺寸和图像大小自动计算帧数，例如dude.png的分辨率为`288*48`，指定`frameWidth`和`frameHeight`后，dude.png在宽度上分为9份，在高度上分为1份，得到9帧图像。

> 想避免使用this指针？使用game.scene.keys.default替代this。

# 显示图像

```javascript
function create (){
    this.add.image(400, 300, 'sky');
    this.add.image(400, 300, 'star');
}
```
400和300是图像坐标的x值和y值，所有游戏对象的定位都默认基于它们的中心点，

# Arcade（游乐场）物理系统（Physics system）


```javascript
var config = {
    physics: {
        default: 'arcade',
        arcade: {
            gravity: { y: 300 },
            debug: false
        }
    }
    // ……
};

var platforms;
function create (){
    platforms = this.physics.add.staticGroup();
    platforms.create(400, 568, 'ground').setScale(2).refreshBody();
    platforms.create(600, 400, 'ground');
    platforms.create(50, 250, 'ground');
    platforms.create(750, 220, 'ground');
    // ……
}
```
在Arcade物理系统中，有动态的和静态的两类物体（body）。动态物体可以通过外力比如速度（velocity）、加速度（acceleration），得以四处移动。它可以跟其他对象发生反弹（bounce）、碰撞（collide），此类碰撞受物体质量和其他因素影响。静态物体只有位置和尺寸，重力对它没有影响，不能给它设置速度，有东西跟它碰撞时，它一点都不动。名副其实，完全是静态的。一般用于设置地面和平台，让玩家在上面来回跑动。

`setScale(2)`把它按x2（两倍）缩放，要调用refreshBody()，这是因为我们缩放的是一个静态物体，所以必须把所作变动告诉物理世界（physics world）。

# 生成玩家
在`create`添加以下下代码来创造一个玩家。

```javascript
player = this.physics.add.sprite(100, 450, 'dude');
player.setBounce(0.2);
player.setCollideWorldBounds(true);

this.anims.create({
    key: 'left',
    frames: this.anims.generateFrameNumbers('dude', { start: 0, end: 3 }),
    frameRate: 10,
    repeat: -1
});

this.anims.create({
    key: 'turn',
    frames: [ { key: 'dude', frame: 4 } ],
    frameRate: 20
});

this.anims.create({
    key: 'right',
    frames: this.anims.generateFrameNumbers('dude', { start: 5, end: 8 }),
    frameRate: 10,
    repeat: -1
});
```
在游戏100*450的位置生成一个物理精灵，并赋予0.2的一点点反弹（bounce）值，跳起后着地时始终会弹起那么一点点。设置了与游戏世界边界（bound）的碰撞，让精灵不能跑出画面边界，或跳出顶边。
将精灵表单的9帧图像分为3个动画，4帧向左跑动，1帧面向镜头，4帧向右跑动。`frameRate: 10`设置动画每秒运行10帧，`repeat: -1`告诉动画要循环播放。

# 物理逻辑
物理精灵在生成时，即被赋予`body`（物体）属性，这个属性指向它的Arcade物理系统的Body。它表示精灵是Arcade物理引擎中的一个物体。
模仿重力效果:

```javascript
// 模仿重力效果
player.body.setGravityY(300)
// 添加碰撞逻辑
this.physics.add.collider(player, platforms);
```
碰撞器（Collider）收两个对象，检测二者之间的碰撞，传入精灵和平台组就能处理与组合以及所有平台的碰撞。

# 动态物理组

```javascript
stars = this.physics.add.group({
    key: 'star',
    repeat: 11,
    setXY: { x: 12, y: 0, stepX: 70 }
});
```
`setXY`用来设置组的12个子项的位置,`step`值用于组生成子项时加以排布。

# 检测与玩家是否重叠

```javascript
this.physics.add.overlap(player, stars, collectStar, null, this);
function collectStar (player, star)
{
    star.disableBody(true, true);
}
```
这会告诉Phaser，要检查玩家与组中任何一颗星星的重叠。如果检测到，他们就会被传递到'collectStar'函数，并对象被设置为不活动、不可见，即将它从显示中移除。

> 碰撞(collider)和重叠(overlap)的区别就在于一个会阻止前进，而另一个则不会

# 键盘控制
Phaser有内置的键盘管理器，把四个属性up, down, left, right（都是Key对象的实例），植入光标（cursor）对象。然后我们要做的就是在update循环中做这样一些轮询：

```javascript
function update (){
    if (cursors.left.isDown){
        player.setVelocityX(-160);
        player.anims.play('left', true);
    }else if (cursors.right.isDown){
        player.setVelocityX(160);
        player.anims.play('right', true);
    }else{
        player.setVelocityX(0);
        player.anims.play('turn');
    } if (cursors.up.isDown && player.body.touching.down){
        player.setVelocityY(-330);
    }
}
```
`setVelocity`设置物体横向（x轴）和纵向（y轴）的速度。值的绝对值越大，速度越快。





