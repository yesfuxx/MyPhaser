

# 1 初始化

```
var game = new Phaser.Game(800, 600, Phaser.CANVAS, '', { preload: preload, create: create, update: update });

function preload(){}
function create(){}
function update(){}
```

# 2

```
function preload()
{
    game.load.image('sky', 'assets/sky.png')
    game.load.sprite('sky', 'assets/sky.png);
    game.load.spritesheet('dude', 'assets/dude.png', 32, 48);
}
```

```
 function create()
 {
 	game.physics.startSystem(Phaser.Physics.ARCADE);
 	
 	game.add.image(0, 0, 'sky');
 	game.add.sprite(0, 0, 'sky');
 	
 	platforms = game.add.group();
 	platforms.enableBody = true;
 }
  
```

# 3

```
game.stage.backgroundColor = '#007236';//设置背景色
game.input.keyboard.createCursorKeys();//创建上下左右四个方向键对象
game.debug.cameraInfo(game.camera, 32, 500);//debug显示 相机视窗大小，相机游戏位置，边界大小
game.input.activePointer.withinGame //bool 鼠标是否在游戏区域
game.physics.startSystem(Phaser.Physics.ARCADE);//开启物理
//开启物理
var sprite = game.add.sprite(400, 550, 'phaser');
game.physics.enable(sprite, Phaser.Physics.ARCADE);
sprite.alpha = 0.5;
sprite.anchor.set(0.5);
sprite.scale.set(0.6);
sprite.inputEnabled = true;// 精灵启动输入系统
// 子弹组
var bullets = game.add.group();
bullets.enableBody = true;
bullets.physicsBodyType = Phaser.Physics.ARCADE;
// 创建10个子弹
bullets.createMultiple(10, 'bullet');
// 从子弹池获取一个子弹，false代表不存在于屏幕中（被kill）
bullet = bullets.getFirstExists(false);
bullet.reset(sprite.x + 6, sprite.y - 8);
bullet.body.velocity.y = -300;
//增加文本
var text_Hello = game.add.text(20, 20, "How are you!", { font: "16px Arial", fill: "#ffffff", align: "center" });
// leftKey现在还在按下，而且按下时间不超过250ms，适合用来做组合键，想想拳皇
if (this.leftKey.downDuration(250)) {}
game.world.randomX//0-设置的游戏最大宽度
game.world.centerX



```

```
// 截取事件，防止按键事件响应浏览器默认行为
game.input.keyboard.addKeyCapture([ Phaser.Keyboard.LEFT, Phaser.Keyboard.RIGHT, Phaser.Keyboard.SPACEBAR ]);
// 捕获鼠标事件，鼠标事件将不会触发浏览器默认行为
game.input.mouse.capture = true;

//按键绑定事件
var key_Fire = game.input.keyboard.addKey(Phaser.Keyboard.ONE);
key_Fire.onDown.add(onFire, this);
function onFire(){}
//抬起；按下；快速点击  //可用于触屏事件
game.input.onUp.add(function() {console.log('UP');});
game.input.onDown.add(function(){})
game.input.onTap.add(onTap, this);// tap可以在手机响应，click不行
function onTap(pointer, doubleTap) {
    if (doubleTap) {    // 是否双击
        if (sprite.key === 'pic1')      // 更换图片
            sprite.loadTexture('pic2');
        else 
            sprite.loadTexture('pic1');
    } else {
        // 单击按下时间需小于game.input.tapRate，否则不会触发单击事件
        //game.input.tapRate= 5000;
        //game.input.doubleTapRate
        sprite.alpha = (sprite.alpha === 0.5) ? 1 : 0.5;
    }
}
  



//判断鼠标是否按下 leftButton  middleButton  rightButton
game.input.activePointer.leftButton.isDown
sprite.input.pointerOver();// 鼠标是否在sprite上
game.input.mouse.locked//得到鼠标locked
sprite.input.enableDrag(true);//允许拖拽，true代表拖拽时鼠标位于精灵中心
```

```
function render() {

    game.debug.text("Over: " + sprite.input.pointerOver(), 32, 32);
    game.debug.text(game.input.mouse.locked, 320, 32);
}
```

