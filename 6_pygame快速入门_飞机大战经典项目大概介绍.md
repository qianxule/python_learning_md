@[TOC]
# 安装

```python
pip install pygame
```
#  快速入门
## 创建图形化窗口
### 游戏的开始和退出
|方法| 说明|
|-|-|
|pygame.init()| 导入并初始化所有 pygame 模块，使用其他模块之前，必须先调用init 方法|
|pygame.quit()| 卸载所有 pygame 模块，在游戏结束之前调用|

实际上就记忆住，在准备开始游戏的时候需要开始加上init 和 最后加上quit

```python
import pygame
pygame.init()
# 编写游戏的代码
print("游戏的代码...")
pygame.quit()
```

### 坐标系和Rect
1. 原点 在 左上角 (0, 0)
2. x 轴 水平方向向 右，逐渐增加
3. y 轴 垂直方向向 下，逐渐增加
4. 一个Rect接口实际上 由位置以及这个矩形自己本身自己的大小构成   Rect(x, y, width, height)

```python
import pygame
hero_rect = pygame.Rect(100, 500, 120, 125)
print("英雄的原点 %d %d" % (hero_rect.x, hero_rect.y))
print("英雄的尺寸 %d %d" % (hero_rect.width, hero_rect.height))
# size 属性会返回矩形区域的 (宽, 高) 元组
print("%d %d" % hero_rect.siz
```

###  创建游戏主窗口
pygame 专门提供了一个 模块 pygame.display 用于创建、管理、游戏窗口

|方法|说明|
|-|-|
|pygame.display.set_mode()| 初始化游戏显示窗口|
|pygame.display.update()| 刷新屏幕内容显示，稍后使用|

set_mode 方法
set_mode(resolution=(0,0), flags=0, depth=0)
1. 作用 —— 创建游戏显示窗口
2. 参数  resolution 指定屏幕的 宽 和 高，默认创建的窗口大小和屏幕大小一
致 flags 参数指定屏幕的附加选项，例如是否全屏等等，默认不需要传
递  depth 参数表示颜色的位数，默认自动匹配
3. 返回值 可以理解为游戏的屏幕，游戏的元素都需要被绘制到游戏的屏幕上

```python
import pygame
import time
pygame.init()
# 创建游戏的窗口 480 * 700
screen = pygame.display.set_mode((480, 700))
while True:
	time.sleep(1)
pygame.quit()
```

## 图像绘制
要在屏幕上 看到某一个图像的内容，需要按照三个步骤：
– 使用 pygame.image.load() 加载图像的数据
– 使用 游戏屏幕 对象，调用 blit 方法 将图像绘制到指定位置
– 调用 pygame.display.update() 方法更新整个屏幕的显示

```python
import pygame
pygame.init()
# 创建游戏的窗口 480 * 700
screen = pygame.display.set_mode((480, 700))
# 绘制背景图像
# 1> 加载图像数据
bg = pygame.image.load("./images/background.png")
# 2> blit 绘制图像
screen.blit(bg, (0, 0))
# 3> update 更新屏幕显示
pygame.display.update()
while True:
	pass
pygame.quit()
```

可以在 screen 对象完成 所有 blit 方法之后，统一调用一次 display.update 方法，同样可以在屏幕上 看到最终的绘制结果，也就是说前面的blit绘画实际上就是将这个背景画到一张图上（内存？）然后只有等你update之后，才会显示到你的电脑之上。

## 游戏循环和游戏时钟与事件捕捉
### 动画的实现原理
1. 跟电影的原理类似，游戏中的动画效果，本质上是快速的在屏幕上绘制图像电影是将多张静止的电影胶片连续、快速的播放，产生连贯的视觉效果！
2. 一般在电脑上 每秒绘制60次（帧），就能够达到非常连续高品质的动画效果

### 游戏循环
游戏一般包括俩个阶段：
1. 游戏初始化：设置游戏窗口 绘制图像的初始位置 设置游戏时钟等等一些资源的加载都在这一块，也就是写在while循环之外的
2. 游戏循环：设置刷新帧率 检测用户交互 更新图像位置 更新屏幕显示等等一些不断更新交互的功能都在循环中显示

### 游戏时钟
1. pygame 专门提供了一个类 pygame.time.Clock 可以非常方便的设置屏幕绘
制速度 —— 刷新帧率
2. 要使用 时钟对象 需要两步：
	1. 在 游戏初始化 创建一个 时钟对象
	2. 在 游戏循环 中让时钟对象调用 tick(帧率) 方法
3. tick 方法会根据 上次被调用的时间，自动设置 游戏循环 中的延时

```python
# 3. 创建游戏时钟对象
clock = pygame.time.Clock()
i = 0
# 游戏循环
while True:
	# 设置屏幕刷新帧率，每秒 60 次 或者你可以把tick当作一个delay的过程，实际上就是1/60就是他的delay时间
	clock.tick(60)
	print(i)
	i += 1

```

样例（一个简单动画的实现）：

```python
import pygame
# 游戏的初始化
pygame.init()
# 创建游戏的窗口 480 * 700
screen = pygame.display.set_mode((480, 700))
# 绘制背景图像
bg = pygame.image.load("./images/background.png")
screen.blit(bg, (0, 0))
# pygame.display.update()
# 绘制英雄的飞机
hero = pygame.image.load("./images/me1.png")
screen.blit(hero, (150, 300))
# 可以在所有绘制工作完成之后，统一调用 update 方法
pygame.display.update()
# 创建时钟对象
clock = pygame.time.Clock()
# 1. 定义 rect 记录飞机的初始位置
hero_rect = pygame.Rect(150, 300, 102, 126)
# 游戏循环 -> 意味着游戏的正式开始！
while True:
	# 可以指定循环体内部的代码执行的频率
	clock.tick(60)
	# 2. 修改飞机的位置
	hero_rect.y -= 1
	# 判断飞机是否飞出界限，飞出界限就继续从底部开始飞行，这里也可以使用%进行操作
	if hero_rect.bottom <= 0:
		hero_rect.y = 700
	# 3. 调用 blit 方法绘制图像
	screen.blit(bg, (0, 0))
	screen.blit(hero, hero_rect)
	# 4. 调用 update 方法更新显示
	pygame.display.update()
	
pygame.quit()
```

### 事件
事件
1. 就是游戏启动后，用户针对游戏所做的操作
2. 例如：点击关闭按钮，点击鼠标，按下键盘… 监听
3. 在 游戏循环 中，判断用户 具体的操作
只有 捕获 到用户具体的操作，才能有针对性的做出响应


代码实现：
pygame 中通过 pygame.event.get() 可以获得用户当前所做动作的事件列表
用户可以同一时间做很多事情

```python
import pygame
# 游戏的初始化
pygame.init()
# 创建游戏的窗口 480 * 700
screen = pygame.display.set_mode((480, 700))
# 绘制背景图像
bg = pygame.image.load("./images/background.png")
screen.blit(bg, (0, 0))
# pygame.display.update()
# 绘制英雄的飞机
hero = pygame.image.load("./images/me1.png")
screen.blit(hero, (150, 300))
# 可以在所有绘制工作完成之后，统一调用 update 方法
pygame.display.update()
# 创建时钟对象
clock = pygame.time.Clock()
# 1. 定义 rect 记录飞机的初始位置
hero_rect = pygame.Rect(150, 300, 102, 126)
# 游戏循环 -> 意味着游戏的正式开始！
while True:
	# 可以指定循环体内部的代码执行的频率
	clock.tick(60)
	# 捕获事件
	event_list = pygame.event.get()
	if len(event_list) > 0:
		print(event_list)
	# 2. 修改飞机的位置
	hero_rect.y -= 1
	# 判断飞机的位置
	if hero_rect.y <= 0:
		hero_rect.y = 700
	# 3. 调用 blit 方法绘制图像
	screen.blit(bg, (0, 0))
	screen.blit(hero, hero_rect)
	# 4. 调用 update 方法更新显示
	pygame.display.update()
	
pygame.quit()
```

针对退出，我们可以在 while 循环中增加如下代码：

```python
for event in pygame.event.get():
	# 判断事件类型是否是退出事件 这个退出事件实际上对应的就是最右上角的那个x(关闭的叉叉)
	if event.type == pygame.QUIT:
		print("游戏退出...")
		# quit 卸载所有的模块
		pygame.quit()
		# exit() 直接终止当前正在执行的程序
		exit()
```

## 精灵和精灵组
暂时略，后面补
