

```
var game = new Phaser.Game(800, 600, Phaser.AUTO, 'phaser-example', { preload: preload, create: create });

function preload() {
    game.load.spritesheet('button', 'assets/buttons/button_sprite_sheet.png', 193, 71);
}

var text;
var button;
var x = 32;
var y = 80;

function create() {
    game.stage.backgroundColor = '#182d3b';
    // 监听加载的事件
    game.load.onLoadStart.add(loadStart, this);
    game.load.onFileComplete.add(fileComplete, this);
    game.load.onLoadComplete.add(loadComplete, this);
    // 按钮
    button = game.add.button(game.world.centerX - 95, 400, 'button', start, this, 2, 1, 0);
    // 文字
    text = game.add.text(32, 32, 'Click to start load', { fill: '#ffffff' });
}

function start() {
    // 资源
    game.load.image('picture1', 'assets/pics/11.jpg');
    game.load.image('picture2', 'assets/pics/22.png');
    // 开始加载
    game.load.start();
    // 按钮隐藏
    button.visible = false;
}

function loadStart() {
    text.setText("Loading ...");    // 开始加载
}

function fileComplete(progress, cacheKey, success, totalLoaded, totalFiles) {
    // 文件加载进度
    text.setText("File Complete: " + progress + "% - " + totalLoaded + " out of " + totalFiles);
    var newImage = game.add.image(x, y, cacheKey);
    newImage.scale.set(0.3);
    x += newImage.width + 20;
    if (x > 700) {
        x = 32;
        y += 332;
    }
}

function loadComplete() {
    // 加载结束
    text.setText("Load Complete");
}
```



```
图片默认的锚点在左上角

// 按钮隐藏
button.visible = false;
// 检查是否在缓存中
var image_bool = game.cache.checkImageKey('image');
```



```
    //  This sprite was created with the Phaser Gen Paint app
    //  also available in the Phaser Examples repo and on the Phaser site.
    var dudeData = [
        '0123456789ABCDEF',//用这个来画图像
        '......1...1.....',
        '........1.......',        
    ];
    game.create.texture('phaserDude', dudeData, 4, 4, 0);
```



# 加载

```
1// 指定一个key，必须在所有image中唯一，再指定一个图片
game.load.image('imageKey', 'assets/sprites/phaser2.png');
game.add.sprite(0, 0, 'imageKey');// 显示图片

2// 加载声音，第二个参数是一个数组，是同一个音效的不同格式的文件
//这里有mp3和ogg(火狐喜欢ogg)，浏览器会依次尝试是否可以加载该音效
game.load.audio('boden', ['assets/audio/boden.mp3', 'assets/audio/boden.ogg']);

3// 加载视频
game.load.video('chrome', 'assets/video/chrome.webm');
var video = game.add.video('chrome');
// 添加视频
var sprite = video.addToWorld(game.world.centerX, game.world.centerY, 0.5, 0.5, 2, 2);
video.play(true);// 循环播放
game.input.onDown.add(function(){video.paused = (video.paused) ? false : true;});// 点击时pause

4//加载一个“老式的”精灵集，37x45大小，18帧
game.load.spritesheet('uniqueKey', 'assets/mummy37x45.png', 37, 45, 18);
var sprite = game.add.sprite(300, 200, 'uniqueKey');// 还是通过sprite加载
sprite.animations.add('walk');// 添加动画
sprite.animations.play('walk', 50, true);// 开始动画

5//json
//{"version":"2.4.4","name":"Amador","released":"15th October 2015"}//json file
game.load.json('version', 'assets/version.json');// 加载json文件
var phaserJSON = game.cache.getJSON('version');// 从cache中获取json
// 文本显示
var text = game.add.text(100, 100, "version: " + phaserJSON.version, { fill: '#ffffff' });
text.setShadow(2, 2, 'rgba(0,0,0,0.5)', 0);
var text2 = game.add.text(100, 200, "Name: " + phaserJSON.name, { fill: '#ffffff' });
text2.setShadow(2, 2, 'rgba(0,0,0,0.5)', 0);

6// 瓦片地图，一般由两个部分组成，图片和数据，数据可以在文件中通过第二个参数传入，也可以是一个变量，通过第三个参数传入，
第四个参数也可以是Phaser.Tilemap.CSV
game.load.tilemap('mario', 'assets/tilemaps/maps/super_mario.json', null, Phaser.Tilemap.TILED_JSON);
game.load.image('tiles', 'assets/tilemaps/tiles/super_mario.png');// 加载对应的图片
var map = game.add.tilemap('mario');// 添加TilesetImage，第一个参数是名字
map.addTilesetImage('SuperMarioBros-World1-1', 'tiles');
var layer = map.createLayer('World1');// 创建一个层



```



# tween

```
sprite = game.add.sprite(0, 0, 'aimg');
tween = game.add.tween(sprite);// 添加一个Tween动画
tween.to({ x: 100 }, 5000, 'Linear', true, 0);// 5秒内线性移动到x为100处
```



# text

```
var text = "textokokok";
var style = { font: "65px Arial", fill: "#ff0044", align: "center" };// 文字样式
var t = game.add.text(game.world.centerX-300, 0, text, style);// 显示文字
```



# physics

```
game.physics.enable(sprite, Phaser.Physics.ARCADE);// 精灵启动物理引擎，才会有body属性
sprite.body.velocity.x = 150;// 设置水平速度150，启动物理引擎，才能设置物速度

function update () {
    // 如果鼠标距离和精灵距离大于8
    if (game.physics.arcade.distanceToPointer(sprite, game.input.activePointer) > 8)
   		// 让精灵以300的速度靠近鼠标(或者Touch)
  		game.physics.arcade.moveToPointer(sprite, 300); 
    else
        sprite.body.velocity.set(0);// 精灵速度设为0
}

```



# Basic

```
// 前面两个参数代表游戏区域 800x600 px
// Phaser.AUTO表示自动选择Canvas或者webGL
// 'phaser-example'表示将游戏区域放在id为phaser-example的元素中
// 最后一个参数指定回调，preload预加载资源，create创建游戏
var game = new Phaser.Game(800, 600, Phaser.AUTO, 'phaser-example', { preload: preload, create: create });

function preload() {
    // 加载一个图片，之后可通过 einstein 来引用
    game.load.image('einstein', 'assets/pics/ra_einstein.png');
}

function create() {
    // 在(0,0)位置显示该图片
    game.add.sprite(0, 0, 'einstein');
}
  
```







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

// 返回图片的所有key
var images = game.cache.getKeys(Phaser.Cache.IMAGE);
var test = game.add.group();
// 随机创建一些精灵，允许拖拽
for (var i = 0; i < 20; i++) {
    var tempSprite = test.create(game.world.randomX, game.world.randomY, game.rnd.pick(images));
    tempSprite.inputEnabled = true;
    tempSprite.input.enableDrag(false, true);//第二个参数 渲染在最前面
}


//////////////////////////////
function create() {
    sprite = game.add.sprite(game.world.centerX, game.world.centerY, 'ball');
    game.physics.enable(sprite, Phaser.Physics.ARCADE);
}
function update() {
    // 鼠标按下时动
    if (game.input.mousePointer.isDown) {
        // 400表示速度
        game.physics.arcade.moveToPointer(sprite, 400);
        // 已经到达鼠标了，就不动了
        if (Phaser.Rectangle.contains(sprite.body, game.input.x, game.input.y)) {
            sprite.body.velocity.setTo(0, 0);
        }
    } else {
        sprite.body.velocity.setTo(0, 0);
    }
}

sprite1.addChild(sprite2);


var tween = game.add.tween(popup.scale).to( { x: 1, y: 1 }, 1000, Phaser.Easing.Elastic.Out, true);


 button = game.add.button(game.world.centerX - 95, 460, 'button', nukeButton, this, 2, 1, 0);
 button.input.useHandCursor = true;//设置鼠标悬停手型
 function nukeButton() {
    button.pendingDestroy = true;    // 下一个update的时候被销毁
}

 // 全屏
 game.scale.scaleMode = Phaser.ScaleManager.SHOW_ALL;
 game.scale.fullScreenScaleMode = Phaser.ScaleManager.EXACT_FIT;
 
// 在移动设备上全屏
if (!game.device.desktop){
	game.input.onDown.add(function(){game.scale.startFullScreen(false);});
}
 
 
// 创建虚拟按键
//game, x, y, key, callback, callbackContext, overFrame, outFrame, downFrame, upFrame
buttonjump = game.add.button(600, 500, 'buttonjump', null, this, 0, 1, 0, 1);
// 相对相机固定
buttonjump.fixedToCamera = true;
buttonjump.events.onInputOver.add(function(){jump=true;});

//翻转
sprite.scale.x = -1;

game.load.spritesheet('mario', 'assets//mario.png', 50, 50);
player = game.add.sprite(350, game.world.height - 150, 'mario');
// key, framesarray, fps,repeat
player.animations.add('walk', [1,2,3,4], 10, true);
player.animations.play('walk');
player.loadTexture('mario', 5);// 换成另外一种贴图
```



# Group

```
var group = game.add.group();// 创建一个组
group.scale.set(1.5);// 设置组的scale

var atari1 = group.create(100, 100, 'atari');
```



# Input



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

game.input.keyboard.addCallbacks(this, null, null, keyPress);，绑定按键事件
/////////////////////////////////////////////////////////////

//判断鼠标是否按下 leftButton  middleButton  rightButton
game.input.activePointer.leftButton.isDown
sprite.input.pointerOver();// 鼠标是否在sprite上
game.input.mouse.locked//得到鼠标locked
sprite.input.enableDrag(true);//允许拖拽，true代表拖拽时鼠标位于精灵中心
game.input.mousePointer.isDown;//bool  鼠标按下
game.input.activePointer.isDown//
game.input.activePointer.withinGame;// bool 鼠标在游戏区域内

 // 拖拽事件
 dragSprite.inputEnabled = true;
 // 第一个参数lockCenter代表是否以鼠标位置居中，第二个参数bringToTop代表是否把拖拽的精灵置顶
 dragSprite.input.enableDrag(false, true);
 dragSprite.events.onDragStart.add(dragStart， this);
 dragSprite.events.onDragUpdate.add(dragUpdate);
 dragSprite.events.onDragStop.add(dragStop);
 function dragUpdate(sprite, pointer, dragX, dragY, snapPoint) {
    angle += 0.01;
    copySprite.x = dragSprite.x + 220 * Math.cos(angle);
    copySprite.y = dragSprite.y + 220 * Math.sin(angle);
    copySprite.rotation = game.physics.arcade.angleToPointer(copySprite);
    
    sprite.key;// sprite拿到被拖拽的精灵
    pointer.x;// pointer拿到拖拽点
}

// 拖动和松手的时候都按照格子
sprite1.input.enableSnap(32, 32, true, true);
// 拖动的时候自由，松开的时候按照格子
sprite2.input.enableSnap(32, 32, false, true);
sprite.events.onDragStop.add(funcName);

sprite.input.allowVerticalDrag = false;// 不允许竖直方向运动
sprite.input.allowHorizontalDrag = false;// 不允许水平方向运动

game.input.activePointer.duration;//鼠标按下时间
game.input.onUp.add(getTime, this);// 鼠标抬起
function getTime(pointer) {
    lastDuration = pointer.duration;// pointer.duration是按下的时长
}

//鼠标按下抬起,鼠标离开sprite再释放，onUp也会触发
sprite.events.onInputDown.add(onDown, this);
sprite.events.onInputUp.add(onUp, this);

//可以设置优先级，数字越大，优先级越高，优先级高的可以优先接受Input事件
sprite.input.priorityID = 2;


 // 创建一个矩形
var bounds = new Phaser.Rectangle(100, 100, 500, 400);
// 画出来
var graphics = game.add.graphics(bounds.x, bounds.y);
graphics.beginFill(0x000077);
graphics.drawRect(0, 0, bounds.width, bounds.height);
dragSprite.input.boundsRect = bounds;//设置精灵的拖拽范围

 // 一个精灵作为边界
 var sprite = game.add.sprite(game.world.centerX, game.world.centerY, 'pic');
 dragSprite.input.boundsSprite = sprite;
 
 // 这样做每次鼠标移动都会检查精确像素，耗费非常大，你也可以仅仅在点击的时候进行精确像素检查，那将会好很多，选择正确的方法
 sprite.input.pixelPerfectOver = true;//精确空隙
 
 
 
 
 // canvas上的mousedown事件
 game.canvas.addEventListener('mousedown', function(){
 	game.input.mouse.requestPointerLock();//锁定鼠标，按下esc才能解开
 });
 
 // Phaser默认允许两点触摸，addPointer可以增加触摸点
 // 注意：iOS上，6点会触发一个app手势，这点我们无能为力
 game.input.addPointer();//多增加一个，就复制几下


```

```
function render() {

    game.debug.text("Over: " + sprite.input.pointerOver(), 32, 32);
    game.debug.text(game.input.mouse.locked, 320, 32);
}
```

