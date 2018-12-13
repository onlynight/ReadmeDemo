微信小游戏开发教程-游戏实现3
==========================

## 对象池

由于游戏过程中会创建很多临时对象，这些对象很快又不再使用，垃圾回收器也能帮我们主动回收这部分垃圾，但是回收时间不可控制，同时增大了创建对象的开销，所以我们使用对象池技术缓存这些不用的对象，当需要使用的时候将这些对象取出来重复利用，从而避免重复创建对象的开销。下面是一个简易对象池的实现：

```javascript
// pool.js
// @author: wyndam
// @email: only.night@qq.com

export default class Pool {

  constructor() {
    this.pool = {}
  }

  put(name, obj) {
    let list = this.pool[name]
    if (list == null) {
      this.pool[name] = []
    }

    this.pool[name].push(obj)
  }

  get(name) {
    let list = this.pool[name]
    if (list != null) {
      return list.shift()
    }

    return null
  }

}
```

一个二维数组用于缓存不同类型的对象。

## 障碍物

游戏中的水管我们将其定义为障碍物，这里有两种实现，一种是上下两个水管作为一个障碍物对象，另外一种是将其看作两个对象。将其看作一个对象就需要重写碰撞检测函数，看作两个对象的话，需要创建一个对象将这两个障碍物组合在一起，各有各的利弊。这里我们将其看作不同两个对象，不用重写碰撞检测方法，只需要与两个障碍物分别做碰撞检测只要有一个碰撞即表示碰撞。

```javascript
// @author: wyndam
// @email: only.night@qq.com

import Sprite from '../base/sprite.js'

const __ = {
  speed: Symbol('speed')
}

export default class EnvItem extends Sprite {

  constructor(imgSrc, x, y, width, height) {
    super(imgSrc, x, y, width, height)
    this.left = 0
    // this[__.speed] = 2
    this[__.speed] = databus.speed
  }

  update() {
    if(!this.visible){
      return
    }
    this.left -= this[__.speed]
  }

  setSpeed(speed){
    this[__.speed] = speed
  }

}
```

这里抽象出一个环境对象，用于移动物体的基本操作。
水管的实现如下：

```javascript
// barrier.js

// @author: wyndam
// @email: only.night@qq.com

import EvnItem from '../runtime/envItem.js'

const BARRIER_IMG_SRC = 'images/pipe_down.png'
const BARRIER_IMG1_SRC = 'images/pipe_up.png'
const BARRIER_WIDTH = 52
const BARRIER_HEIGHT = 320

export default class Barrier extends EvnItem {

  constructor() {
    super(BARRIER_IMG_SRC, 0, 0, BARRIER_WIDTH, BARRIER_HEIGHT)

    this.left = 0
  }

  init(imgSrc, x, y, width, height) {
    this.img.src = imgSrc
    this.x = x
    this.y = y
    this.start = x
    this.width = width
    this.height = height

    this.visible = true
    this.left = 0
  }

  setLeft(left) {
    this.left = left
  }

  update() {
    if (!this.visible) {
      return
    }

    super.update()

    this.x = this.start + this.left

    if (this.x <= -(window.innerWidth + this.width)) {
      databus.recycleBarrier(this)
    }
  }

}
```

在 update 中移动 x 坐标即可，然后之后update方法会在主循环中调用。

### 障碍物组合

```javascript
// barrierPair.js
// @author: wyndam
// @email: only.night@qq.com

import Sprite from '../base/sprite.js'
import Barrier from '../runtime/barrier.js'

const BARRIER_IMG_SRC = 'images/pipe_down.png'
const BARRIER_IMG1_SRC = 'images/pipe_up.png'
const BARRIER_WIDTH = 52
const BARRIER_HEIGHT = 320

export default class BarrierPair extends Sprite {

  constructor() {
    super(BARRIER_IMG_SRC, 0, 0, BARRIER_WIDTH, BARRIER_HEIGHT)

    this.width = px2dp(this.width) / 1.3
    this.height = px2dp(this.height) / 1.3

    this.left = 0
    this.blank = 100
    this.scored = false

    this.topBarrier = new Barrier()
    this.bottomBarrier = new Barrier()
  }

  init(barrierTopImg, barrierBottomImg, x, y, blank) {
    this.topBarrier.init(barrierTopImg, x, y, this.width, this.height)
    this.bottomBarrier.init(barrierBottomImg, x, y + this.topBarrier.height + blank, this.width, this.height)

    this.blank = blank
    this.visible = true
    this.scored = false
    this.left = 0
  }

  draw(ctx) {
    if (!this.visible) {
      return
    }

    this.topBarrier.draw(ctx)
    this.bottomBarrier.draw(ctx)
  }

  update() {
    if (!this.visible) {
      return
    }

    this.topBarrier.update()
    this.bottomBarrier.update()
  }

  /**
   * 分别对两个障碍物做碰撞检测即可
   */
  isCollideEdgeWith(target) {
    return (this.topBarrier.isCollideEdgeWith(target) || this.bottomBarrier.isCollideEdgeWith(target))
  }

  /**
   * 判断玩家是否越过了障碍物
   */
  isPassed(player) {
    if (this.scored) {
      return false
    }
    let score = (player.x > this.topBarrier.x + this.topBarrier.width)
    if (score) {
      this.scored = true
    }
    return score
  }

}
```

### 障碍物生成器

这里障碍物会不断的从右边出来，所以我们要不断的生成障碍物，需要有一个能够管理障碍物的工具：

```javascript
// filaname: barrier-manager.js
// @author: wyndam
// @email: only.night@qq.com

import BarrierPair from './barrierPair.js'

export default class BarrierManager {

  constructor() {}

  /**
   * 根据帧数，间隔一段时间生成一个障碍物，并将其加如全局缓存中
   */
  generateBarriers(frame) {
    if (frame % databus.barrierGenFrame === 0) {
      let barrier = databus.generateBarrier('images/pipe_down.png', 'images/pipe_up.png',
        window.innerWidth, px2dp(-130) + Math.random() * px2dp(100), px2dp(130))

      databus.barriers.push(barrier)
    }
  }

  draw(ctx) {
    for (let i = 0; i < databus.barriers.length; i++) {
      databus.barriers[i].draw(ctx)
    }
  }

  update() {
    for (let i = 0; i < databus.barriers.length; i++) {
      databus.barriers[i].update()
    }
  }

}
```

全局缓存实现如下：

```javascript
// filename: databus.js
// @author: wyndam
// @email: only.night@qq.com

import Pool from './base/pool.js'
import BarrierPair from './runtime/barrierPair.js'

window.RATIO = window.innerWidth / 288

window.px2dp = function(px) {
  return px * RATIO
}

let instance

export default class DataBus {

  constructor() {
    if (instance == null) {
      instance = this
    } else {
      return instance
    }

    // 从开始到现在的帧数
    this.frame = 0
    // 游戏是否在运行，是否需要更新
    this.running = true
    // 游戏是否结束
    this.gameOver = false
    // 障碍物显示队列
    this.barriers = []
    // 缓存对象池
    this.pool = new Pool()

    // 全局难度参数
    this.speed = 2 // 速度
    this.barrierGenFrame = 80 // 生成障碍物间隔帧数
  }

  /**
   * 从添加到绘制的障碍物列表中回收不显示的用于新障碍物的显示
   */
  recycleBarrier(barrier) {
    if (barrier != null) {
      barrier.visible = false
      let temp = this.barriers.shift()
      temp.visible = false
      this.barriers[0].left -= this.speed
      this.pool.put('barrier', temp)
    }
  }

  /**
   * 从对象池中去除障碍物组合对象，没有的话就创建一个
   */
  generateBarrier(barrierTop, barrierBottom, x, y, blank) {
    let barrier = this.pool.get('barrier')

    if (barrier != null) {
      barrier.init(barrierTop, barrierBottom, x, y, blank)
      return barrier
    } else {
      let temp = new BarrierPair()
      temp.init(barrierTop, barrierBottom, x, y, blank)
      return temp
    }
  }

}

window.databus = new DataBus()
```

全局对象中包含缓存对象池以及一些全局控制变量
