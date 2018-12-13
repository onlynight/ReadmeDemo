微信小游戏开发教程-游戏实现1
==========================

## 概述

微信开发者工具官方提供一个飞机大战的游戏Demo，这里我们不再使用这个demo，我们以FlappyBird为例，为了让读者更加容易理解。

## 源码

https://github.com/onlynight/FlappyBird

强烈建议读者根据教程自己实现一遍游戏，这让能让你更加熟悉开发的流程和代码的原理；在一些不清楚的地方可以参考源码实现的方法。

## 游戏开发

首先我们在 src 目录下新建一个 base 目录用于存放基类，然后创建一个基类 ```Sprite``` ，这个类中会包含游戏的的一些通用功能，例如2d绘图，碰撞检测等。

### 1. canvas#drawImage()

这个方法是2D游戏的核心绘图函数，用于将图片绘制到canvas上。

```javascript
ctx.drawImage(
  this.img,
  this.x,
  this.y,
  this.width,
  this.height
)
```

### 2. 碰撞检测

碰撞检测对游戏体验影响比较大，有多种碰撞检测方法：

#### a. 中心点检测法

这种检测方法代码最简单，只需要判断中心点是否在目标矩形区域内即可：

```javascript
isCollideWith(target) {
	let targetX = target.x + target.width / 2
	let targetY = target.y + target.height / 2

	return (targetX >= this.x &&
	  targetX <= this.x + this.width &&
	  targetY >= this.y &&
	  targetY <= this.y + this.height)
}
```

#### b. 边缘碰撞检测法

图片都是矩形，当两个矩形有任何一部分重合即视为碰撞：

```javascript
isClollideEdgeWith(target) {
	return ((target.x >= this.x &&
	    target.x <= this.x + this.width &&
	    target.y >= this.y &&
	    target.y <= this.y + this.height) || // top left

	  (target.x + target.width >= this.x &&
	    target.x + target.width <= this.x + this.width &&
	    target.y >= this.y &&
	    target.y <= this.y + this.height) || // top right

	  (target.x >= this.x &&
	    target.x <= this.x + this.width &&
	    target.y + target.height >= this.y &&
	    target.y + target.height <= this.y + this.height) || // bottom left

	  (target.x + target.width >= this.x &&
	    target.x + target.width <= this.x + this.width &&
	    target.y + target.height >= this.y &&
	    target.y + target.height <= this.y + this.height)) // bottom right
}
```

### Sprite 完整代码源码如下：

```javascript
// @author: wyndam
// @email: only.night@qq.com

/**
 * 精灵类，包含绘图以及碰撞检测等
 */
export default class Sprite {

  constructor(imgSrc, x, y, width, height) {
    // 当前对象的坐标以及尺寸
    this.x = x
    this.y = y
    this.width = width
    this.height = height

    // 当前对象显示的图片
    this.img = new Image()
    this.img.src = imgSrc

    // 标识当前对象是否显示
    this.visible = true
  }

  /**
   * 将图片绘制到 canvas 上
   * {@param ctx cancas context 对象}
   */
  drawToCanvas(ctx) {
    if (!this.visible) {
      return
    }

    ctx.drawImage(
      this.img,
      this.x,
      this.y,
      this.width,
      this.height
    )
  }

  /**
   * 中心点检测
   * {@param target 目标物体}
   */
  isCollideWith(target) {
    if (!this.visible || !target.visible) {
      return
    }

    let targetX = target.x + target.width / 2
    let targetY = target.y + target.height / 2

    return (targetX >= this.x &&
      targetX <= this.x + this.width &&
      targetY >= this.y &&
      targetY <= this.y + this.height)
  }

  /**
   * 严格边缘检测，会误判
   * {@param target 目标物体}
   */
  isClollideEdgeWith(target) {
    if (!this.visible || !target.visible) {
      return
    }

    return ((target.x >= this.x &&
        target.x <= this.x + this.width &&
        target.y >= this.y &&
        target.y <= this.y + this.height) || // top left

      (target.x + target.width >= this.x &&
        target.x + target.width <= this.x + this.width &&
        target.y >= this.y &&
        target.y <= this.y + this.height) || // top right

      (target.x >= this.x &&
        target.x <= this.x + this.width &&
        target.y + target.height >= this.y &&
        target.y + target.height <= this.y + this.height) || // bottom left

      (target.x + target.width >= this.x &&
        target.x + target.width <= this.x + this.width &&
        target.y + target.height >= this.y &&
        target.y + target.height <= this.y + this.height)) // bottom right
  }

}
```

## 创建渲染循环

window.requestAnimationFrame() 方法告诉浏览器您希望执行动画并请求浏览器在下一次重绘之前调用指定的函数来更新动画。该方法使用一个回调函数作为参数，这个回调函数会在浏览器重绘之前调用。

	注意：若您想要在下次重绘时产生另一个动画画面，您的回调例程必须调用 requestAnimationFrame()。

在 ```main.js``` 中：

我们创建一个 loop 函数，然后在 loop 函数中调用 requestAnimationFrame 方法，然后在初始化时再调用一次 requestAnimationFrame ，并且将 requestAnimationFrame 的执行指向 loop函数，这样就实现了循环。

```javascript
// @author: wyndam
// @email: only.night@qq.com

let ctx = canvas.getContext('2d')

export default class Main {

  constructor() {

    this.onCreate()
    
  }

  onCreate(){

    this.bindLoop = this.loop.bind(this)
    window.cancelAnimationFrame(this.aniId);

    this.aniId = window.requestAnimationFrame(
      this.bindLoop,
      canvas
    )
  }

  loop(){
    
    this.aniId = window.requestAnimationFrame(
      this.bindLoop,
      canvas
    )

  }

}
```


## 绘制背景

接下来我们绘制游戏的背景，先绘制一个静态的背景，然后我们再让背景动起来。

1. 新建 runtime 包
2. 新建一个 background.js 文件
3. 新建一个 Background 类，然后将背景填入默认参数

```javascript
// @author: wyndam
// @email: only.night@qq.com

import Sprite from '../base/sprite.js'

const BG_IMG_SRC = 'images/bg_day.png'
const BG_IMG_WIDTH = 288
const BG_IMG_HEIGHT = 512

export default class Background extends Sprite {

  constructor() {
    super(BG_IMG_SRC, 0, 0, BG_IMG_WIDTH, BG_IMG_HEIGHT)
  }

}
```

以上静态背景类就创建好了，然后我们将它添加到绘制流程中去。

4. 在 ```main.js``` 中添加一个新的属性 ```this.bg = new Background()``` 
5. 在 ```loop``` 函数中调用绘制函数即可 ```this.bg.drawToCanvas()```，添加后的代码如下：

```javascript
// @author: wyndam
// @email: only.night@qq.com

import Background from './runtime/background.js'

let ctx = canvas.getContext('2d')

export default class Main {

  constructor() {

    this.onCreate()
    
  }

  onCreate(){
    this.bg = new Background()

    this.bindLoop = this.loop.bind(this)
    window.cancelAnimationFrame(this.aniId);

    this.aniId = window.requestAnimationFrame(
      this.bindLoop,
      canvas
    )
  }

  loop(){

    this.render()
    
    this.aniId = window.requestAnimationFrame(
      this.bindLoop,
      canvas
    )

  }

  render(){
    this.bg.drawToCanvas(ctx)
  }

}
```

这时候保存一下代码，你就可以看到背景图绘制到屏幕上了。


接着我们让背景动起来，先在背景类的构造函数中添加一个位移变量：

```javascript
this.left = 0
```

然后我们修改一下绘制函数，根据前面我们提到的无限循环背景原理，我们这里需要绘制两张背景图：

```javascript
drawToCanvas(ctx) {

	ctx.drawImage(
	  this.img,
	  this.x + this.left,
	  this.y,
	  window.innerWidth,
	  window.innerHeight
	)

	ctx.drawImage(
	  this.img,
	  this.x + window.innerWidth + this.left,
	  this.y,
	  window.innerWidth,
	  window.innerHeight
	)

}
```

接着，添加一个更新函数，动态修改 ```this.left``` 的值，让背景动起来：

```javascript
update(){
	this.left+=2
}
```

其中 ```this.left``` 的增长率就是背景移动的速度，你可以自己修改一下这个值看下效果。

最后在 ```main.js``` loop函数中调用更新函数即可让背景动起来，修改后的代码如下：

```javascript
// main.js
// @author: wyndam
// @email: only.night@qq.com

import Background from './runtime/background.js'

let ctx = canvas.getContext('2d')

export default class Main {

  constructor() {

    this.onCreate()
    
  }

  onCreate(){
    this.bg = new Background()

    this.bindLoop = this.loop.bind(this)
    window.cancelAnimationFrame(this.aniId);

    this.aniId = window.requestAnimationFrame(
      this.bindLoop,
      canvas
    )
  }

  loop(){

    this.update()
    this.render()
    
    this.aniId = window.requestAnimationFrame(
      this.bindLoop,
      canvas
    )

  }

  render(){
    this.bg.drawToCanvas(ctx)
  }

  update(){
    this.bg.update()
  }

}
```
