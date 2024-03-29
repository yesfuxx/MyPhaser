# phaser3 Notes



### event



```
sprite.once('pointerdown', function () {
//  Sprite will have visible = false set when the animation finishes repeating because of 'hideOnComplete' property
	this.play('explode');
});
//////////////////////

this.anims.create({
    key: 'idle',
    frames: this.anims.generateFrameNames('knight', { prefix: 'idle/frame', start: 0, end: 5, zeroPad: 4 }),
    frameRate: 8,
    repeat: -1
});
var lancelot = this.add.sprite(500, 536);
this.input.on('pointerdown', function () {
    if (lancelot.anims.getName() === 'idle')
    	lancelot.play('run');
    else if (lancelot.anims.getName() === 'run')
   		lancelot.play('idle');
});
```





### temp

```
//  This will bring the Logo Scene to the top of the Scene List
this.scene.bringToTop('Scene2');//放置最上层

//声音失去焦点暂停
this.sound.pauseOnBlur = true;
this.load.audio('dafunk', [
    'assets/audio/Dafunk - Hardcore Power (We Believe In Goa - Remix).ogg',
    'assets/audio/Dafunk - Hardcore Power (We Believe In Goa - Remix).mp3',
    'assets/audio/Dafunk - Hardcore Power (We Believe In Goa - Remix).m4a'
]);
//], { stream: true });

value.toFixed(2) //保持两位数

//得到image key
var keys = this.textures.getTextureKeys();
for (var i = 0; i < keys.length; i++){
    var x = Phaser.Math.Between(0, 800);
    var y = Phaser.Math.Between(0, 600);
    this.add.image(x, y, keys[i]);
}

var colors = [ 0xef658c, 0xff9a52, 0xffdf00, 0x31ef8c, 0x21dfff, 0x31aade, 0x5275de, 0x9c55ad, 0xbd208c ];
//get an random item form Array
Phaser.Utils.Array.GetRandom(colors)

//深度
sprite.setDepth(y);

//设置颜色
const colors = [ 0xef658c, 0xff9a52, 0xffdf00, 0x31ef8c, 0x21dfff, 0x31aade, 0x5275de, 0x9c55ad, 0xbd208c ];
const sprite1 = this.add.sprite(200, 300, 'cube').setTint(colors[0]);
```



```
var config = {
    type: Phaser.AUTO,
    parent: 'phaser-example',
    scene: {
        create: create
    }
};

var game = new Phaser.Game(config);

function create ()
{
    //  1) Decode the xml
    var xml = Phaser.DOM.ParseXML(atob(base64EncodedXML));

    //  2) Create an Image element from the encoded png:
    var image = new Image();

    var _this = this;

    image.onload = function ()
    {
        //  4) Successful decode? Then create the texture:
        var texture = _this.textures.addImage('desyrel', image);

        //  5) Create the Bitmap Font data
        var data = Phaser.GameObjects.BitmapText.ParseXMLBitmapFont(
            xml,
            _this.textures.getFrame('desyrel'),
            0, 0,
            texture
        );

        //  6) Add the bitmap font entry - the structure of the object is important
        _this.cache.bitmapFont.add('desyrel', { data: data, texture: 'desyrel', frame: null });

        //  Now we're free to create bitmap text objects, like this ...
        _this.add.bitmapText(0, 0, 'desyrel', 'Bitmap Text Decoded from base64');
    };

    //  3) Decode the image
    image.src = base64EncodedPNG;
}
var base64EncodedXML = '...'
var base64EncodedPNG = '...'
```



## 0 Example

```
<!doctype html> 
<html lang="en"> 
<head> 
    <meta charset="UTF-8" />
    <title>Making your first Phaser 3 Game - Part 10</title>
    <script src="//cdn.jsdelivr.net/npm/phaser@3.11.0/dist/phaser.js"></script>
    <style type="text/css">
        body {
            margin: 0;
        }
    </style>
</head>
<body>

<script type="text/javascript">

var config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    physics: {
        default: 'arcade',
        arcade: {
            gravity: { y: 300 },
            debug: false
        }
    },
    scene: {
        preload: preload,
        create: create,
        update: update
    }
};

var player;
var stars;
var bombs;
var platforms;
var cursors;
var score = 0;
var gameOver = false;
var scoreText;

var game = new Phaser.Game(config);

function preload ()
{
    this.load.image('sky', 'assets/sky.png');
    this.load.image('ground', 'assets/platform.png');
    this.load.image('star', 'assets/star.png');
    this.load.image('bomb', 'assets/bomb.png');
    this.load.spritesheet('dude', 'assets/dude.png', { frameWidth: 32, frameHeight: 48 });
}

function create ()
{
    //  A simple background for our game
    this.add.image(400, 300, 'sky');

    //  The platforms group contains the ground and the 2 ledges we can jump on
    platforms = this.physics.add.staticGroup();

    //  Here we create the ground.
    //  Scale it to fit the width of the game (the original sprite is 400x32 in size)
    platforms.create(400, 568, 'ground').setScale(2).refreshBody();

    //  Now let's create some ledges
    platforms.create(600, 400, 'ground');
    platforms.create(50, 250, 'ground');
    platforms.create(750, 220, 'ground');

    // The player and its settings
    player = this.physics.add.sprite(100, 450, 'dude');

    //  Player physics properties. Give the little guy a slight bounce.
    player.setBounce(0.2);
    player.setCollideWorldBounds(true);

    //  Our player animations, turning, walking left and walking right.
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

    //  Input Events
    cursors = this.input.keyboard.createCursorKeys();

    //  Some stars to collect, 12 in total, evenly spaced 70 pixels apart along the x axis
    stars = this.physics.add.group({
        key: 'star',
        repeat: 11,
        setXY: { x: 12, y: 0, stepX: 70 }
    });

    stars.children.iterate(function (child) {

        //  Give each star a slightly different bounce
        child.setBounceY(Phaser.Math.FloatBetween(0.4, 0.8));

    });

    bombs = this.physics.add.group();

    //  The score
    scoreText = this.add.text(16, 16, 'score: 0', { fontSize: '32px', fill: '#000' });

    //  Collide the player and the stars with the platforms
    this.physics.add.collider(player, platforms);
    this.physics.add.collider(stars, platforms);
    this.physics.add.collider(bombs, platforms);

    //  Checks to see if the player overlaps with any of the stars, if he does call the collectStar function
    this.physics.add.overlap(player, stars, collectStar, null, this);

    this.physics.add.collider(player, bombs, hitBomb, null, this);
}

function update ()
{
    if (gameOver)
    {
        return;
    }

    if (cursors.left.isDown)
    {
        player.setVelocityX(-160);

        player.anims.play('left', true);
    }
    else if (cursors.right.isDown)
    {
        player.setVelocityX(160);

        player.anims.play('right', true);
    }
    else
    {
        player.setVelocityX(0);

        player.anims.play('turn');
    }

    if (cursors.up.isDown && player.body.touching.down)
    {
        player.setVelocityY(-330);
    }
}

function collectStar (player, star)
{
    star.disableBody(true, true);

    //  Add and update the score
    score += 10;
    scoreText.setText('Score: ' + score);

    if (stars.countActive(true) === 0)
    {
        //  A new batch of stars to collect
        stars.children.iterate(function (child) {

            child.enableBody(true, child.x, 0, true, true);

        });

        var x = (player.x < 400) ? Phaser.Math.Between(400, 800) : Phaser.Math.Between(0, 400);

        var bomb = bombs.create(x, 16, 'bomb');
        bomb.setBounce(1);
        bomb.setCollideWorldBounds(true);
        bomb.setVelocity(Phaser.Math.Between(-200, 200), 20);
        bomb.allowGravity = false;

    }
}

function hitBomb (player, bomb)
{
    this.physics.pause();

    player.setTint(0xff0000);

    player.anims.play('turn');

    gameOver = true;
}

</script>

</body>
</html>
```



## 1 GAME CONFIG



```
var config = {
    type: Phaser.AUTO,
    parent: 'phaser-example',
    width: 800,
    height: 600,
    scene: {
        preload: preload,
        create: create
    },
    //  Open the Dev Tools
    //  The text in the banner will be in white on a neon pink background.
    //  The colors at the start of the background array define the blocks
    //  at the beginning of the banner
    title: 'Shock and Awesome',
    banner: {
        text: '#ffffff',
        background: [
            '#fff200',
            '#38f0e8',
            '#00bff3',
            '#ec008c'
        ],
        hidePhaser: true
    }
};
```

### 1.1 Game Rng

```
var seed = 1419463258969;

var config = {
    type: Phaser.AUTO,
    parent: 'phaser-example',
    width: 800,
    height: 600,
    seed: [ seed ],
    scene: {
        create: create
    }
};

var game = new Phaser.Game(config);

function create ()
{
    var text = this.add.text(80, 80, '', { font: '16px Courier', fill: '#ffffff' });

    //  Every time you reload the page you should get the same values back again
    //  because the RNG was seeded in the Game Config with the same seed value.
    //  Adjust the seed for different results. Remove it for random results.

    var shuffleTest = [ 'A', 'B', 'C', 'D', 'E', 'F', 'G' ];

    text.setText([
        'RNG',
        '---',
        'Seed: ' + seed,
        '',
        'Between 0 and 50: ' + Phaser.Math.RND.between(0, 50),
        'Between 0 and 1: ' + Phaser.Math.RND.realInRange(0, 1),
        'Normal: ' + Phaser.Math.RND.normal(),
        'UUID: ' + Phaser.Math.RND.uuid(),
        'Angle: ' + Phaser.Math.RND.angle(),
        'Shuffle: ' + Phaser.Math.RND.shuffle(shuffleTest).join(' ')
    ]);
}
```



### bitmaptext

```
this.load.bitmapFont('desyrel', 'assets/fonts/bitmap/desyrel.png', 'assets/fonts/bitmap/desyrel.xml');
this.add.bitmapText(0, 0, 'desyrel', 'Lorem ipsum\ndolor sit amet', 50);


```

## Load



```
this.load.pack('pack', 'assets/loader-tests/pack6.json');//数据存放在json

this.load.image('sukasuka-chtholly', 'http://labs.phaser.io/assets/pics/sukasuka-chtholly.png');
this.load.image('sukasuka-chtholly', 'assets/pics/sukasuka-chtholly.png');
this.load.image({
    key: 'taikodrummaster', 
    url: 'assets/pics/taikodrummaster.jpg'
});
this.load.path = 'assets/pics/';
this.load.image([
    { key: 'sukasuka-chtholly' },
    { key: 'taikodrummaster', extension: 'jpg' }
]);

//可以加载法线贴图
this.load.image('robot', [ 'assets/pics/equality.png', 'assets/normal-maps/equality.png' ]);
this.load.image({
    key: 'robot', 
    url: 'assets/pics/equality-by-ragnarok.png',
    normalMap: 'assets/normal-maps/equality.png'
});

var bg = this.add.image(0, 0, 'buttonBG').setInteractive();
bg.once('pointerup', onLoadImage, this);
function loadImage(){
    this.load.once('complete', addSprites, this);//加载完成，执行
    this.load.image('pic', 'assets/pics/turkey-1985086.jpg');
    this.load.start();//开始加载
}
function addSprites(){this.add.image(400, 300, 'pic');}

this.load.once('filecomplete', showFile, this);//每个加载完成，执行
function showFile (key, type, texture){
    var x = Phaser.Math.Between(0, 800);
    var y = Phaser.Math.Between(0, 600);
    this.add.image(x, y, key);
}

this.load.on('filecomplete', function (key, file) {//每个加载完成，执行
	this.add.image(400, 300, key);
}, this);

var keys = this.textures.getTextureKeys();//得到所有图片key

var config = {
    type: Phaser.AUTO,
    width: 800,
    height: 600,
    parent: 'phaser-example',
    pixelArt: true,
    scene: {
        preload: preload,
        create: create,
        pack: {
            files: [
                { type: 'image', key: 'bear', url: 'assets/pics/alex-bear.png' }
            ]
        }
    }
};
var game = new Phaser.Game(config);
function preload (){
    this.add.image(400, 300, 'bear').setScale(2);
    this.load.on('fileprogress', function (file, value) {

    // progress.clear();
    if (file.key === 'aasset'){
    	progress.fillRect(400, 500 - (value * 400), 60, value * 400);
    }
}

//load spritesheet
this.load.spritesheet('explosion', 'assets/sprites/explosion.png', { frameWidth: 64, frameHeight: 64, endFrame: 23 });
this.load.spritesheet('balls', 'assets/sprites/balls.png', { frameWidth: 17, frameHeight: 17 });
var config = {
key: 'explodeAnimation',
frames: this.anims.generateFrameNumbers('explosion', { start: 0, end: 23, first: 23 }),
frameRate: 20,
repeat: -1
};
this.anims.create(config);
this.add.sprite(400, 300, 'explosion').play('explodeAnimation');
this.add.sprite(400, 300, 'balls', 3);

```



### light2d

```
function create ()
{
    this.add.image(-300, 0, 'robot').setOrigin(0).setPipeline('Light2D');
    var light  = this.lights.addLight(0, 0, 200);
    this.lights.enable().setAmbientColor(0x555555);
    this.input.on('pointermove', function (pointer) {
        light.x = pointer.x;
        light.y = pointer.y;
    });
}
```



### camera

```
var controls;

var game = new Phaser.Game(config);

function preload ()
{
    this.load.image('tiles', 'assets/tilemaps/tiles/cybernoid.png');
    this.load.tilemapTiledJSON('map', 'assets/tilemaps/maps/cybernoid.json');
}

function create ()
{
    var map = this.make.tilemap({ key: 'map' });

    var tiles = map.addTilesetImage('cybernoid', 'tiles');

    var layer = map.createLayer(0, tiles, 0, 0);

    this.cameras.main.setBounds(0, 0, map.widthInPixels, map.heightInPixels);

    var cursors = this.input.keyboard.createCursorKeys();

    var controlConfig = {
        camera: this.cameras.main,
        left: cursors.left,
        right: cursors.right,
        up: cursors.up,
        down: cursors.down,
        speed: 0.5
    };

    controls = new Phaser.Cameras.Controls.FixedKeyControl(controlConfig);
}

function update (time, delta)
{
    controls.update(delta);
}

```



### toJson

```
this.add.text(400, 32, 'Check the console', { color: '#00ff00' }).setOrigin(0.5, 0);

this.anims.create({ key: 'diamond', frames: this.anims.generateFrameNames('gems', { prefix: 'diamond_', end: 15, zeroPad: 4 }), repeat: -1 });
this.anims.create({ key: 'prism', frames: this.anims.generateFrameNames('gems', { prefix: 'prism_', end: 6, zeroPad: 4 }), repeat: -1 });
this.anims.create({ key: 'ruby', frames: this.anims.generateFrameNames('gems', { prefix: 'ruby_', end: 6, zeroPad: 4 }), repeat: -1 });
this.anims.create({ key: 'square', frames: this.anims.generateFrameNames('gems', { prefix: 'square_', end: 14, zeroPad: 4 }), repeat: -1 });

this.add.sprite(400, 200, 'gems').play('diamond');
this.add.sprite(400, 300, 'gems').play('prism');
this.add.sprite(400, 400, 'gems').play('ruby');
this.add.sprite(400, 500, 'gems').play('square');

//  Get a JSON representation of a single animation, or all animations:

//  You can extract the animation:
var ruby = this.anims.get('ruby');

//  Then pass it to JSON.stringify
console.log(JSON.stringify(ruby));

//  Or call toJSON directly (this returns an Object)
console.log(ruby.toJSON());

//  You can also call 'this.anims.toJSON' and pass it the key of the animation you want:
console.log(JSON.stringify(this.anims.toJSON('ruby')));

//  Or dump out ALL animations in the Animation Manager:
console.log(JSON.stringify(this.anims));
```





### anims



```

this.load.spritesheet('boom', 'assets/sprites/explosion.png', { frameWidth: 64, frameHeight: 64, endFrame: 23 });

//  Our 'boom' spritesheet has 23 frames in it.
//  This animation will use them all by just giving it the sprite sheet key:
const config1 = {
    key: 'explode1',
    frames: 'boom',
    frameRate: 20,
    repeat: -1
};

//  Here we use the 'generateFrameNumbers' function instead to set the start and end frame:
const config2 = {
    key: 'explode2',
    frames: this.anims.generateFrameNumbers('boom', { start: 0, end: 23 }),
    frameRate: 20,
    repeat: -1
};

//  Here we use the 'frames' array because we want to specify the exact frames to use:
const config3 = {
    key: 'explode3',
    frames: this.anims.generateFrameNumbers('boom', { frames: [ 0, 1, 2, 1, 2, 3, 4, 0, 1, 2 ] }),
    frameRate: 20,
    repeat: -1
};

this.anims.create(config1);
this.anims.create(config2);
this.anims.create(config3);

this.add.sprite(200, 300, 'boom').play('explode1');
this.add.sprite(400, 300, 'boom').play('explode2');
this.add.sprite(600, 300, 'boom').play('explode3');





/////////////
sprite.anims.getName()==='idle'






```



```
this.anims.create({ key: 'walk', frames: this.anims.generateFrameNames('zombie', { prefix: 'walk_', end: 8, zeroPad: 3 }), repeat: -1, frameRate: 8 });
```



#### 播放

```
this.rob = this.add.sprite(400, 560, 'zombie').setOrigin(0.5, 1).play('walk');
this.rob.anims.playAfterDelay('death', 2000);
this.rob.anims.playAfterRepeat('turn');

//  When the current animation repeat ends, we'll play the 'turn' animation
ripley.anims.playAfterRepeat('turn');
//  And after that, the 'walk' look
ripley.anims.chain('walk');

/////////////////
//  Our global 'spin' animation
this.anims.create({
    key: 'spin',
    frames: this.anims.generateFrameNames('cube', { prefix: 'frame', start: 1, end: 23 }),
    frameRate: 50,
    repeat: -1
});
//  Play the 'spin' animation
sprite1.play({ key: 'spin' });
//  Play the animation and override the default frameRate with a new one
sprite2.play({ key: 'spin', frameRate: 20 });
//  Play the animation and set the repeatDelay to 250ms
sprite3.play({ key: 'spin', repeatDelay: 250 });
```





#### 事件

```
// Event handler for when the animation completes on our sprite
ANIMATION_COMPLETE//动画播放完成
ANIMATION_REPEAT//动画重复
ANIMATION_START
ANIMATION_STOP
ANIMATION_UPDATE
sprite.on(Phaser.Animations.Events.ANIMATION_COMPLETE, function () {
    this.releaseItem();
}, this);
sprite.on(Phaser.Animations.Events.ANIMATION_UPDATE, function (anim, frame, sprite, frameKey) {
    //  We can run our effect when we get frame0004:
    if (frameKey === 'attack_B/frame0004')
   		this.releaseItem();
}, this);
```





#### 暂停和恢复

```

this.input.on('pointerdown', function () {
//  Every single animation in the Animation Manager will be paused:
    if (this.anims.paused)
        this.anims.resumeAll();
    else
        this.anims.pauseAll();
}, this);
/////////////////////////////////////////////
const square = this.anims.create({ key: 'square', frames: this.anims.generateFrameNames('gems', { prefix: 'square_', end: 14, zeroPad: 4 }), repeat: -1 });
//  square added twice just to make sure there are more of them
const keys = [ 'diamond', 'prism', 'ruby', 'square', 'square' ];
let x = 100;
let y = 116;
for (let i = 0; i < 8; i++){
	this.add.sprite(x, y, 'gems').play(keys[Phaser.Math.Between(0, 4)]);
}
this.input.on('pointerdown', function () {
//  Every sprite using the global 'square' animation will now pause,
//  because we're pausing the Animation instance itself:
if (square.paused)
	square.resume();
else
	square.pause();
```



#### 移除

```
 const square = this.anims.create({ key: 'square', frames: this.anims.generateFrameNames('gems', { prefix: 'square_', end: 14, zeroPad: 4 }), repeat: -1 });
this.add.sprite(x, y, 'gems').play('square');
//  We'll now remove the square animation from the global Animation Manager
this.anims.remove('square');
```







```
this.tweens.add({
    targets: item,
    props: {
        y: {
            value: -64,
            ease: 'Linear',
            duration: 3000,
        },
        x: {
            value: '+=128',
            ease: 'Sine.inOut',
            duration: 500,
            yoyo: true,
            repeat: 4
           }
    },
    onComplete: function () {
    	item.destroy();
    }
});
```





### tilesprite



```
this.bg = this.add.tileSprite(0, 16, 800, 600, 'bg').setOrigin(0);
this.ground = this.add.tileSprite(0, 536, 800, 64, 'tiles', 1).setOrigin(0);
update (){
    this.bg.tilePositionX += 4;
    this.ground.tilePositionX += 8;
}
```





### keyboard

```
this.input.keyboard.on('keydown-SPACE', function (event) {
this.sprite.play('walk');
}, this);

this.input.keyboard.on('keydown-Y', function (event) {
this.sprite.anims.yoyo = !this.sprite.anims.yoyo;
}, this);

this.input.keyboard.on('keydown-Q', function (event) {
		this.sprite.playReverse('walk');
}, this);

this.input.keyboard.on('keydown-R', function (event) {
this.sprite.anims.reverse();
}, this);
```





### Group

```
const group = this.add.group();
group.createMultiple({ key: 'diamonds', frame: 0, repeat: 279 });
Phaser.Actions.GridAlign(group.getChildren(), { width: 20, height: 20, cellWidth: 38, x: 38, y: 50 });
this.anims.staggerPlay('flash', group.getChildren(), 60);

```



### tweens

```
this.tweens.add({
    targets: this.lancelot.anims,
    timeScale: { from: 0.5, to: 2 },
    ease: 'Sine.inOut',
    yoyo: true,
    repeat: -1,
    repeatDelay: 1000,
    hold: 1000,
    duraton: 3000
});
```

### Action

```
const group = this.add.group({
    key: 'diamonds',
    frame: [ 0, 1, 2, 3, 4 ],
    frameQuantity: 20  //每帧的个数
});
Phaser.Actions.GridAlign(group.getChildren(), {
    width: 10,	//宽多少个
    height: 10,	//高多少个
    cellWidth: 32,
    cellHeight: 32,
    x: 100,
    y: 100
});

//////////////
this.groupA.create(100 + Math.random() * 600, 100 + Math.random() * 400, 'atlas', 'veg0' + Math.floor(1 + Math.random() * 9));

//////
Phaser.Actions.IncX(this.groupA.getChildren(), Math.cos(this.move));
Phaser.Actions.IncY(this.groupA.getChildren(), Math.sin(this.move));
Phaser.Actions.Rotate(this.groupA.getChildren(), -0.01);


//////////
const circle = new Phaser.Geom.Circle(400, 300, 220);//x,y,r
this.group = this.add.group({
	key: 'balls', 
	frame: [0, 1, 5], 
	repeat: 10//每帧个数
});
Phaser.Actions.PlaceOnCircle(this.group.getChildren(), circle);//放置到圆
this.tween = this.tweens.addCounter({
    from: 220,
    to: 100,
    duration: 3000,
    delay: 2000,
    ease: 'Sine.easeInOut',
    repeat: -1,
    yoyo: true
});
update ()
{
Phaser.Actions.RotateAroundDistance(this.group.getChildren(), { x: 400, y: 300 }, 0.02, this.tween.getValue());
}

//////////////
create (){
    this.group1 = this.add.group({ key: 'ball', frameQuantity: 36 });
    this.circle1 = new Phaser.Geom.Circle(400, 300, 200);
    Phaser.Actions.PlaceOnCircle(this.group1.getChildren(), this.circle1);
}
update (){
	Phaser.Actions.RotateAroundDistance(this.group1.getChildren(), this.circle1, -0.030, this.circle1.radius);
}



```



#### Place On ellipse（在线上）

```
///////////////////
his.ellipse = new Phaser.Geom.Ellipse(400, 300, 200, 500);x,y,w,h
this.group = this.add.group({ key: 'ball', frameQuantity: 48 });
Phaser.Actions.PlaceOnEllipse(this.group.getChildren(), this.ellipse);//放置到椭圆
this.tweens.add({
    targets: this.ellipse,
    width: 700,
    height: 100,
    delay: 1000,
    duration: 2000,
    ease: 'Sine.easeInOut',
    repeat: -1,
    yoyo: true
});
update (){
	Phaser.Actions.PlaceOnEllipse(this.group.getChildren(), this.ellipse);
}
```

#### Place On Line

```
const line = new Phaser.Geom.Line(100, 200, 600, 400);x1,y1,x2,y2
const group = this.add.group({ key: 'ball', frameQuantity: 32 });
Phaser.Actions.PlaceOnLine(group.getChildren(), line);
```

#### Place On Rectangle

```
const rect = new Phaser.Geom.Rectangle(100, 100, 256, 256);//x,y,w,h
const group = this.add.group({ key: 'ball', frameQuantity: 32 });
Phaser.Actions.PlaceOnRectangle(group.getChildren(), rect);
```

#### Place On Rectangle Shift

```
create (){
    this.rect = new Phaser.Geom.Rectangle(64, 32, 100, 512);
    this.group = this.add.group({ key: 'balls', frame: [0,1,2,3,4,5], frameQuantity: 10 });
    this.tweens.add({
    targets: this.rect,
    x: 200,
    y: 200,
    width: 512,
    height: 100,
    delay: 2000,
    duration: 3000,
    ease: 'Sine.easeInOut',
    repeat: -1,
    yoyo: true
    });
}
update (){
    Phaser.Actions.PlaceOnRectangle(this.group.getChildren(), this.rect, this.i);//shiftnumber positional offset
    this.i++;
    if (this.i === this.group.length)
        this.i = 0;
}
```

#### Random（面上）

```
this.load.image('orb', 'assets/sprites/orb-blue.png');
//  Create 300 sprites (they all start life at 0x0)
const group = this.add.group({ key: 'orb', frameQuantity: 300 });
const circle = new Phaser.Geom.Circle(400, 300, 130);

//  Randomly position the sprites within the circle
Phaser.Actions.RandomCircle(group.getChildren(), circle);//圆
Phaser.Actions.RandomEllipse(group.getChildren(), this.ellipse);椭圆
Phaser.Actions.RandomLine(group.getChildren(), line);//
Phaser.Actions.RandomRectangle(group.getChildren(), rect);//
Phaser.Actions.RandomTriangle(group.getChildren(), triangle);//
```

#### Rotate Around

```
this.load.spritesheet('diamonds', 'assets/sprites/diamonds32x24x5.png', { frameWidth: 32, frameHeight: 24 });
this.group = this.add.group();
for (var i = 0; i < 256; i++){
this.group.create(Phaser.Math.Between(200, 600), Phaser.Math.Between(100, 500), 'diamonds', Phaser.Math.Between(0, 4));
}
update (){
	Phaser.Actions.RotateAround(this.group.getChildren(), { x: 400, y: 300 }, 0.01);
}
```

#### Rotate Around Distance

```
this.group = this.add.group();
for (let i = 0; i < 32; i++){
	this.group.create(i * 32, i * 2, 'ball');
}
update (){
	Phaser.Actions.RotateAroundDistance(this.group.getChildren(), { x: 400, y: 300 }, 0.02, 200);
}
```

#### Rotate Around X Y

```
create ()
{
	this.group = this.add.group();
    for (var i = 0; i < 256; i++)    {	
    		this.group.create(Phaser.Math.Between(200, 600), Phaser.Math.Between(100, 500), 'diamonds', 			  	Phaser.Math.Between(0, 4));
    }
    this.geomPoint = new Phaser.Geom.Point(400, 300);
    this.input.on('pointermove', function (pointer) {
    	this.geomPoint.setTo(pointer.x, pointer.y);
    }, this);
}
update (){
	Phaser.Actions.RotateAroundDistance(this.group.getChildren(), this.geomPoint, 0.1, 100);
}
```

#### Rotate Container Facing Point

```
class Example extends Phaser.Scene{
    constructor ()    {
        super();
        this.container;
        this.center = {x: 400, y: 300}
        this.rotateSpeed = 0.02
    }
    preload ()    {
        this.load.spritesheet('diamonds', 'assets/sprites/diamonds32x24x5.png', { frameWidth: 32, frameHeight: 24 });
    }
    create ()    {
        this.add.sprite(this.center.x, this.center.y, 'diamonds', 1); // center point. We will rotate around it

        this.container = this.add.container(600, 300);

        const text = this.add.text(-25, -50, 'Phaser');

        const diamond1 = this.add.sprite(0, 0, 'diamonds', 1);
        diamond1.setScale(2)

        const diamond2 = this.add.sprite(15, 0, 'diamonds', 2);
        diamond2.setScale(2)

        const diamond3 = this.add.sprite(-15, 0, 'diamonds', 3);
        diamond3.setScale(2)

        this.container.add([diamond1, diamond2, diamond3, text])

        // stop rotation on click
        this.input.on('pointerdown', function() {
          if (this.rotateSpeed > 0) {
              this.rotateSpeed = 0
          } else {
              this.rotateSpeed = 0.02
          }
        }, this);
    }

    update ()    {
        Phaser.Actions.RotateAroundDistance([this.container], this.center, this.rotateSpeed, 250);
        //面向旋转中心
        const angleDeg = Math.atan2(this.container.y - this.center.y, this.container.x - this.center.x) * 180 / Math.PI;
        this.container.angle = angleDeg+90 // container should face the center point
    }
}
```

#### Set Alpha

```
const group = this.add.group({ key: 'diamonds', frame: 0, frameQuantity: 50, setXY: { x: 32, y: 32, stepX: 14 }});
//SetAlpha(group.getChildren(), value, step)
Phaser.Actions.SetAlpha(group.getChildren(), 0, 1 / 50);
```

#### Spread

```
const group = this.add.group({ key: 'diamonds', frame: 3, frameQuantity: 50, setXY: { x: 32, y: 32, stepX: 14 }});
//  Spread out the children between the 2 given values, using the string-based property
Phaser.Actions.Spread(group.getChildren(), 'alpha', 0, 1);
```

#### Set X Y

```
const group = this.add.group();
//  Add an existing Image into the group:
const image = this.add.image(0, 0, 'phaser');
group.add(image);
//  For example this will set the position of the image to 400 x 300
Phaser.Actions.SetXY(group.getChildren(), 400, 300);
```

#### Shift Position

```
class Example extends Phaser.Scene
{
    constructor ()    {
        super();
        this.move = 0;
        this.x = 0;
        this.y = 0;
    }
    preload ()    {
        this.load.image('sky', 'assets/skies/deepblue.png');
        this.load.image('ball', 'assets/demoscene/ball-tlb.png');
    }
    create ()    {
        this.add.image(0, 0, 'sky').setOrigin(0);
        this.group = this.add.group({ key: 'ball', frameQuantity: 128 });
        
        this.input.on('pointermove', function (pointer) {
            this.x = pointer.x;
            this.y = pointer.y;
        }, this);
    }
    update (time, delta)	{
        this.move += delta;
        if (this.move > 6)        {
            Phaser.Actions.ShiftPosition(this.group.getChildren(), this.x, this.y);
            this.move = 0;
        }
    }
}
```

#### Wrap In Camera Bounds

```
class Example extends Phaser.Scene
{
    constructor ()    {
        super();
    }

    create ()    {
        this.graphics = this.add.graphics();

        this.shapes = new Array(15).fill(null).map(
            () => new Phaser.Geom.Circle(Phaser.Math.Between(0, 800), Phaser.Math.Between(0, 600), Phaser.Math.Between(25, 75))
        );

        this.rect = Phaser.Geom.Rectangle.Clone(this.cameras.main);
    }

    update ()    {
        this.shapes.forEach(function (shape, i) {
            shape.x += (1 + 0.1 * i);
            shape.y += (1 + 0.1 * i);
        });

        Phaser.Actions.WrapInRectangle(this.shapes, this.rect, 72);

        this.draw();
    }

    // Locals methods, they are not part of Phaser.scene
    color (i)    {
        return 0x001100 * (i % 15) + 0x000033 * (i % 5);
    }

    draw ()    {
        this.graphics.clear();

        this.shapes.forEach((shape, i) => {
            this.graphics
            .fillStyle(this.color(i), 0.5)
            .fillCircleShape(shape);
        }, this);
    }
}
```

#### Wrap In Rectangle

```
class Example extends Phaser.Scene
{
    constructor ()    {
        super();
    }
    preload ()    {
        this.load.image('ball', 'assets/sprites/shinyball.png');
    }
    create ()    {
        this.rect = new Phaser.Geom.Rectangle(100, 100, 256, 256);
        this.group = this.add.group({ key: 'ball', frameQuantity: 32 });

        Phaser.Actions.RandomRectangle(this.group.getChildren(), this.rect);
    }
    update ()    {
        this.children = this.group.getChildren();
	    //IncXY(group.getChildren)  x,y 增量
        Phaser.Actions.IncXY(this.children, 1, 1);
        Phaser.Actions.WrapInRectangle(this.children, this.rect);
    }
}
```

