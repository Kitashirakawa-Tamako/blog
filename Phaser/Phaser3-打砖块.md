本文介绍如何使用Phaser3开发一个打砖块（breakout）小游戏，使用方向键“左”、“右”来控制平台反弹小球击打全部的砖块，请先[体验一下](/phaser3/breakout/index.html)。

# 资源准备
从任意素材网获取以下素材：砖块、小球以及平台的图片。

创建index.html并引入phaser.js，当前版本：3.60.0。

# 初始化

```javascript
const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    physics: {
        default: 'arcade'
    },
    scene: {
        preload: preload,
        create: create,
        update: update
    }
}

function preload() {
}

function create() {
}

function update() {
}
```
此时，页面中会显示一个800*600的黑色画布。
# 引入素材并绘制图像
定义砖墙（bricks）、平台（paddle）和球（ball）的全局变量
```javascript
let bricks, paddle, ball
function preload() {
    this.load.image('blue', 'assets/element_blue_rectangle.png');
    this.load.image('green', 'assets/element_green_rectangle.png');
    this.load.image('purple', 'assets/element_purple_rectangle.png');
    this.load.image('red', 'assets/element_red_rectangle.png');
    this.load.image('yellow', 'assets/element_yellow_rectangle.png');
    this.load.image('grey', 'assets/element_grey_rectangle.png');
    this.load.image('paddle', 'assets/paddleBlu.png');
    this.load.image('ball', 'assets/ballGrey.png');
}
function create() {
    bricks = this.physics.add.staticGroup({
        key: ['blue', 'green', 'purple', 'red', 'yellow', 'grey'],
        frameQuantity: 10,
        gridAlign: {width: 10, height: 6, cellWidth: 64, cellHeight: 32, x: 80, y: 50}
    })
    paddle = this.physics.add.sprite(400, 550, 'paddle');
    ball = this.physics.add.image(400, 520, 'ball');
}
```
在物理系统中添加一个静态组，由'blue', 'green', 'purple', 'red', 'yellow', 'grey'6帧图像组成，并且每个帧连续生成10次。再定义图像帧在宽度上个数为10、高度上个数为6，像素为64*32、并在系统中设置合适的位置。
在画布底部添加一个精灵当作玩家控制平台，并在平台上方绘制一个小球。

刷新页面能看到图片素材已经被我们整合进了游戏中。
![微信截图_20231102122640.png](/image/60fe8baa-6c3e-451a-ad5a-1e4610469523.png)

# 游戏逻辑实现
## 键盘输入
设计思路：当按下“←”键时，平台向左运动；按下“右”键时，平台向右运动；按下“空格”键时，小球发射出去。
在全局变量中添加键盘管理器（cursors）

```javascript
let bricks, paddle, ball, cursors
```
在create方法中对cursors对象进行初始化
```javascript
cursors = this.input.keyboard.createCursorKeys();
```
在update方法中轮询检测键盘事件，当“空格”键按下时将小球的横向速度设置为(-200, 200)区间的随机数，纵向速度设置为-300；并在“←”、“→”按键的事件中设置平台的速度为-500/500。
```javascript
function update() {
    if (cursors.space.isDown) {
        ball.setVelocity(Phaser.Math.Between(-200, 200), -300);
    }
    if (cursors.left.isDown) {
        paddle.setVelocityX(-500);
    } else if (cursors.right.isDown) {
        paddle.setVelocityX(500);
    } else {
        paddle.setVelocityX(0);
    }
}
```
现在，当我们按下方向键时，平台会左右移动，当我们按下空格键时，球就会向上发射。
![GIF.gif](/image/ececb51d-753b-461d-b237-2c69f7c05f23.gif)
可以看到，目前的游戏具有以下几个问题：
1. 当小球发射前，小球没有跟随平台一起运动
2. 当小球发射后，点击空格仍然可以改变小球的横向速度

解决方法：使用一个对象记录游戏状态，当游戏开始前，小球应固定在平台上一起运动；游戏开始后，点击空格不能在为小球添加横向的运动速度。

在全局变量中添加游戏状态（gameStart）
```javascript
let bricks, paddle, ball, cursors, gameStart
```
并修改update方法中空格键的判断
```javascript
function update() {
    if (!gameStart) {
        ball.x = paddle.x
        if (cursors.space.isDown) {
            gameStart = true;
            ball.setVelocity(Phaser.Math.Between(-200, 200), -300);
        }
    }
}
```
![GIF.gif](/image/543496d2-37f7-4ba4-b0d6-3125af054d4a.gif)
目前键盘输入已经完成，接下来我们为系统添加碰撞逻辑。
## 碰撞检测
### 游戏边界的碰撞
我们需要给游戏世界边界设置碰撞检测，使得平台的运动不能超过左右的边界，小球运动到上、左、右的边界上会发生反弹，允许小球掉落超过下方的边界。

在create方法中设置游戏边界是否允许发生碰撞，setBoundsCollision([left], [right], [up], [down])，并对小球和平台设置与边界发生碰撞的属性setCollideWorldBounds(true)，再对小球设置反弹（bounce）值为1，表示小球碰撞后速度保持不变。
```javascript
this.physics.world.setBoundsCollision(true, true, true, false);

paddle = this.physics.add.sprite(400, 550, 'paddle').setCollideWorldBounds(true);

ball = this.physics.add.image(400, 520, 'ball').setCollideWorldBounds(true).setBounce(1);
```
![GIF1.gif](/image/257334f1-ac0f-4326-a237-36f14771ac0a.gif)
### 小球与平台的碰撞
现在我们为小球和平台之间添加碰撞检测，当小球撞击平台左侧，则给小球向左的速度；当撞击平台右侧，则给小球向右的速度；当小球正好撞击平台中心时，给小球一个随机的速度。小球撞击位置越偏，则反弹的横向速度越大。

另外，需要给平台设置不可移动的属性（immovable），否则当小球撞击平台时，平台将会被弹开，在create方法中添加小球与平台的碰撞检测。
```javascript
paddle = this.physics.add.sprite(400, 550, 'paddle').setCollideWorldBounds(true).setImmovable();

this.physics.add.collider(ball, paddle, hitPaddle);
```
在全局中添加碰撞事件hitPaddle。
```javascript
function hitPaddle(ball, paddle) {
    if (ball.x === paddle.x) {
        ball.setVelocityX(Phaser.Math.Between(2, 10));
    } else {
        ball.setVelocityX(10 * (ball.x - paddle.x));
    }
}
```
![GIF2.gif](/image/58baf38e-7a3a-476a-949f-4268de31c980.gif)
### 小球与砖块的碰撞
当小球撞击砖块时砖块消失，在create方法中添加小球与砖块的碰撞检测。
```javascript
this.physics.add.collider(ball, bricks, hitBricks);
```
在全局中添加碰撞事件hitBricks。
```javascript
function hitBricks(ball, brick) {
    brick.disableBody(true, true)
}
```
![GIF3.gif](/image/28f5e341-20de-4a8f-a58e-7c813089c4a5.gif)
## 游戏开始/结束
当玩家按下空格时游戏开始，当小球撞击所有砖块后或者小球掉落超过边界时游戏结束。当游戏未开始时，在屏幕中显示“Press SPACE to Start”提示玩家如何开始游戏，游戏开始后文字隐藏；当玩家赢得游戏时，屏幕中显示“You won”；当玩家输掉游戏时，屏幕中显示“Game Over”。

在全局中定义生成文本内容的公共方法initText，第一个参数为文本的内容，第二个参数控制文本显示或隐藏。文本对象的排布与游戏对象不同，所有游戏对象的定位都默认基于它们的中心点而文本对象的定位基于左上角，所以重置文本对象的原点为中心点使得其显示在游戏中心。
```javascript
function initText(text, visible = true) {
    return this.add.text(
        this.physics.world.bounds.width / 2,
        this.physics.world.bounds.height / 2,
        text,
        {
            fontSize: '50px',
            fill: '#fff'
        }
    ).setOrigin(.5).setVisible(visible);
}
```
在全局变量中添加三个变量openingText、gameOverText、playerWonText。
```javascript
let bricks, paddle, ball, cursors, gameStart, openingText, gameOverText, playerWonText
```
在create方法中创建三个文本对象，并通过call的方式调用initText函数，注意方法中this的指向问题。
```javascript
openingText = initText.call(this, 'Press SPACE to Start')
gameOverText = initText.call(this, 'Game Over', false)
playerWonText = initText.call(this, 'You won', false)
```
修改update方法中的代码，当游戏开始时隐藏“Press SPACE to Start”，当玩家赢得游戏时显示“You won”，当玩家输掉游戏时显示“Game Over”。
```javascript
function update() {
    if (!gameStart) {
        ball.x = paddle.x
        if (cursors.space.isDown) {
            gameStart = true;
            openingText.visible = false
            ball.setVelocity(Phaser.Math.Between(-200, 200), -300);
        }
    }
    if (cursors.left.isDown) {
        paddle.setVelocityX(-500);
    } else if (cursors.right.isDown) {
        paddle.setVelocityX(500);
    } else {
        paddle.setVelocityX(0);
    }
    if (bricks.countActive() === 0) {
        playerWonText.visible = true
        game.destroy()
    } else if (ball.body.y > this.physics.world.bounds.height) {
        gameOverText.visible = true
        game.destroy()
    }
}

```
![GIF.gif](/image/c75500d2-8321-4f67-9440-bf3e9d52ed52.gif)
![GIF1.gif](/image/1ad27712-0e5e-4d47-8143-cab3e21829b4.gif)
# 完整js代码
```javascript
let bricks, paddle, ball, cursors, gameStart, openingText, gameOverText, playerWonText
const config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    physics: {
        default: 'arcade'
    },
    scene: {
        preload: preload,
        create: create,
        update: update
    }
}

function preload() {
    this.load.image('blue', 'assets/element_blue_rectangle.png');
    this.load.image('green', 'assets/element_green_rectangle.png');
    this.load.image('purple', 'assets/element_purple_rectangle.png');
    this.load.image('red', 'assets/element_red_rectangle.png');
    this.load.image('yellow', 'assets/element_yellow_rectangle.png');
    this.load.image('grey', 'assets/element_grey_rectangle.png');
    this.load.image('paddle', 'assets/paddleBlu.png');
    this.load.image('ball', 'assets/ballGrey.png');
}

function create() {
    this.physics.world.setBoundsCollision(true, true, true, false);

    bricks = this.physics.add.staticGroup({
        key: ['blue', 'green', 'purple', 'red', 'yellow', 'grey'],
        frameQuantity: 10,
        gridAlign: {width: 10, height: 6, cellWidth: 64, cellHeight: 32, x: 80, y: 50}
    })

    paddle = this.physics.add.sprite(400, 550, 'paddle').setCollideWorldBounds(true).setImmovable();

    ball = this.physics.add.image(400, 520, 'ball').setCollideWorldBounds(true).setBounce(1);

    cursors = this.input.keyboard.createCursorKeys();

    this.physics.add.collider(ball, paddle, hitPaddle);
    this.physics.add.collider(ball, bricks, hitBricks);

    openingText = initText.call(this, 'Press SPACE to Start')
    gameOverText = initText.call(this, 'Game Over', false)
    playerWonText = initText.call(this, 'You won', false)
}

function update() {
    if (!gameStart) {
        ball.x = paddle.x
        if (cursors.space.isDown) {
            gameStart = true;
            openingText.visible = false
            ball.setVelocity(Phaser.Math.Between(-200, 200), -300);
        }
    }
    if (cursors.left.isDown) {
        paddle.setVelocityX(-500);
    } else if (cursors.right.isDown) {
        paddle.setVelocityX(500);
    } else {
        paddle.setVelocityX(0);
    }
    if (bricks.countActive() === 0) {
        playerWonText.visible = true
        game.destroy()
    } else if (ball.body.y > this.physics.world.bounds.height) {
        gameOverText.visible = true
        game.destroy()
    }
}

function hitPaddle(ball, paddle) {
    if (ball.x === paddle.x) {
        ball.setVelocityX(Phaser.Math.Between(2, 10));
    } else {
        ball.setVelocityX(10 * (ball.x - paddle.x));
    }
}

function hitBricks(ball, brick) {
    brick.disableBody(true, true)
}

function initText(text, visible = true) {
    return this.add.text(
        this.physics.world.bounds.width / 2,
        this.physics.world.bounds.height / 2,
        text,
        {
            fontSize: '50px',
            fill: '#fff'
        }
    ).setOrigin(.5).setVisible(visible);
}

const game = new Phaser.Game(config)
```