@[toc]

# 数据科学领域概述
## 数据如何驱动运营给企业带来价值
1. 原始收集数据
数据埋点（数据埋点的意思就是用户使用的时候我们采集数据的地方，比如用户鼠标悬停在某张图片的时间，这就可以当作一个数据埋点）收集用户在网页端，APP，小程序等终端的各种数据
业务数据
外部数据
2. 数据加工处理
将收集的数据转换成可理解、可量化、可观察的业务指标
单纯的数据没有意义，只有和业务结合才能发挥价值
3. 数据可视化
有了数据指标，必须管理好指标
建立指标体系
4. 数据决策和执行
从数据中得到相关信息，需要把这些信息转换成策略
包括策略制定，并持续优化和改进策略
5. 数据产品
将策略制作成数据应用和产品
开始自动化和系统化运营
6. 数据战略
积累了大量数据，大量模型，大量数据应用
不只是数据分析，可以将数据变现

![[image/ee3f77f6e273e91dd918673e891ca63e_MD5.png]]
## 岗位
一般存在下面三种岗位：
1. 业务线 数据分析
2. 研发线 数据仓库（数据仓库是完全为数据分析人员服务的一个数据库，存储的数据，用户行为路径，新增用户数等）
3. 算法线 数据挖掘（相对于数据分析，需要建模，模型的目的是做预测）


## 关键词说明
1. ETL 清洗 转换 加载
2. DW 数据仓库
3. CRM 客户关系管理
4. CMS:Content Management System "内容管理系统
5. KPI 绩效


## 业务的商业模式
1. B2C ： 面向客户的商业模式（比如游戏）
2. B2B ： 面向企业的商业模式（比如淘宝，实际上就是向商家收钱）
3. B2B2C：面向企业也面向客户的商业模式（比如京东）
4. B2VC：这种就是拿投资人的钱（吐槽的hh）


# 数据指标
## 数据指标定义及常用数据指标
1. 数据指标的定义：对当前业务==有参考价值==的==可统计==数据

2. 常用的数据指标
用户数据 谁
行为数据 干了什么
业务数据 产生了什么结果
3. 用户数据
存量 DAU、MAU
增量 新增用户
健康程度 留存率
从哪儿来 渠道来源
搜索引擎推广 SEO
Rom 推广
app 商店（自然流量 也可以推广）
手机厂商预装
其它产品挂下载链接
扫码
4. 行为数据
次数、频率 PV UV 访问深度
关键路径走了多远 转化率
行为做了多久 时长
质量 弹出率(跳出率) 
5. 业务数据
总量 GMV 访问时长
人均 ARPU AverageRevenuePerUser 每用户平均收入 人均访问时长
ARPPU Average Revenue Per Paying User 每付费用户平均收益
人数 付费人数 播放人数
健康程度 付费率 付费频次 观看率
被消费对象 SKU 被消费内容

## 如何选取指标
最终目的出发 梳理业务模块 -> 判断业务模块类型->根据业务模块类型选择
数据指标：

如何梳理业务模块
1. 目的 比如我要卖货
2. 实现目的的方法 我通过文章来卖货（如何搞大/搞频繁(手段)）
3. 方法需要的工具 通过社区创作文章来卖货，我提供一个制作精美图文的工具
4. 实现方法的途径

业务模块：
![[image/8ae778035a08df011ad422013286c8eb_MD5.png]]
根据业务模块选择数据指标
 1. 工具模块：效率
 2. 内容浏览模块：质和量
 3. 交易模块：转化率
 4. 社区模块：活跃度  

## 分析角度
![[image/a2723f930601ea6878cb7af7474c5bb2_MD5.png]]

### 计数
计数阶段：

可以使用 goaccess 这款工具简单的来统计

通过脚本与代码统计日志
通过 BI 工具进行基本的分析
通过 awk 工具去搞，外加 uniq 去重，wc 去统计

```python
awk '{print $1}' access.log|sort|uniq|wc -l
```

使用awk 计算pv（总点击量，直接wc -l access.log就是计算总点击量） ，uv（人访问量，上面的代码就是统计每ip访问量）

### 流量导向的工具
解决的问题
流量依赖性业务,如电商,或者一锤子买卖
优势
能将流量入口分析得较为细致


### 内容导向的工具
解决的问题
哪些资源被消费
被消费的情况如何
内容表现质量如何
解决的问题
以内容为核心资源的,如媒体、视频网站

优势
能从内容的视角描述其表现


### 用户导向的工具
解决的问题
用户来了干什么?
用户还会不会再来?
用户在哪流失了?
用户都是啥样的?

Mixpanel 工具
==Inspectlet 工具== ---通过录制屏幕的行为 还原用户的行为

### 业务导向的工具
解决的问题
流程是否顺畅?
规模/频次如何?
异常原因何在?

这里可以看下神策数据的，能进入这家公司挺好的。

# 数据分析方法
## 对比分析
如何进行对比分析：
事出反常必有妖，不论增减，只要用户不正常上升下降都需要对比

对比分析比什么：
1. 绝对值
本身具备价值的数字
销售金额
阅读数 微信的 10 万+
2. 比例值
在具体环境中看比例，才具备对比价值
活跃占比
注册转化率
3. 绝对值
不易得知内在问题，比例值，易收到极端值影响 2%~4%怎么比
4. 环比 7 号 6 号 5 号 7 月 6 月 5 月
对短期内具备连续性的数据进行分析
5. 同比 今年国庆销售额 去年国庆销售额
观察更为长期的数据集
观察的时间周期里有较多干扰，希望某种程度上消除这些干扰
6. 和自己比
从时间维度 环比|同比
7. 从不同业务线
8. 从过往经验估计
9. 和同行业比：是自身因素还是行业趋势

## 多维分析
 例子：
 
公司做了微博大 V 推广，想看情况

基本思路：
数据怎么样
有 XX 人启动过
APP 关键功能使用率 XXX%
日活和留存是 

思路流程：
APP 启动 按设备 iPhone 美图手机比较多 符合产品定位
APP 启动 按来源 用户因 PUSH 下发进入 APP 比较多
APP 启动 按城市等级查看 发现一线城市用户比较多
运营能力有限只有北上广深有推送，因此打开几率大
APP 启动 按新老用户查看 日活量整体变化不大，老用户占比下降，新用户占比上升，留不住用户。

多维分析就是将一个事件分解成多个小的事件，然后逐层去分析讨论

## 漏斗分析
运作原理
通过一连串向后影响的用户行为来观察目标

适用场景
适用： 有明确的业务流程和业务目标
不太适用：没有明确的流程，跳转关系复杂

漏斗分析就是归结原因，比如我要分析这个软件为什么没人用，第一层宣传是否到位，宣传到位了，再分析安装是否到位，注册是否到位，等等有一系列的时间顺序下去的流程。和多维分析需要区分开

## 留存分析
验证产品长期价值可以看月留存，将某一时间段的用户 ID 与另一交叉去重。

随着产品不断的优化，月留存是不断增长的

看日留存会数据量特别多，观察重点不知道放在哪里，而一个月我们产品一个迭代，发一个新版本

一般的计算方式 看大盘可能不准，产品 运营 技术 市场每个环节可能都会对留存造成影响，比如搞活动，引入了一个低质量的渠道，造成留存大跌，因为低质量渠道进来的都是垃圾用户，因此需要看精准留存

精准留存
过滤进行过指定行为的用户 ID，再计算
将用户分为不同的群体后，观察之前留存的区别

## 总结
数据分析基础阶段

数据分析指标：
![[image/51026ec8d1fd679adce79c976b266582_MD5.png]]
跳过率就是跳出率
GMV：总的访问时长
ARPU：每用户平均访问时长
ARPPU：每付费用户平均访问时长
SKU：品类的数量

四个模块：
![[image/c6eae1dcdf202143b6ba965fbeba2334_MD5.png]]
转化率就是详情页转化率，就是点击那个详情页占比总点进list页面的比率（就是商品更吸引人）。

分析工具：
![[image/810501193b95c63350c9ec26df03fcaf_MD5.png]]
数据分析方法：
还是注意多维分析和漏斗分析的区别
![[image/c4acfdba703af4417c6d8defbc37720d_MD5.png]]

# 用户画像
用户画像：通过对用户各类特征进行标识给用户贴上各类==标签==，通过这些标签将用户分为不同的群体，以便对不同的群体分别进行产品/运营动作用途。

## 标签有什么

基础属性
1. 年龄
2. 性别
3. 生日
4. 星座
5. 教育
6. 身高
7. 收入
8. 职业

社会关系
1. 婚姻
2. 有无小孩
3. 有无女孩
4. 家有老人
5. 性取向

行为特征
1. 基本行为
2. 注册时间
3. 来源渠道
4. 业务行为
5. 买过特惠商品

业务相关---比如是健身类的产品，就会有下面的业务指标
1. 胖瘦高矮
2. 体脂率
3. 在练胸
4. 日均 8000 步
5. 收藏了 100+份健身计划

## 如何获得标签
1. 用户直接填写
	就是用户注册的账号的信息。
2. 通过用户的行为进行推测
	比如这个用户他购买的物品寄到北京，那么大概率他的地址就在北京，这样子。
3. 通过用户身边的人推断
	距离相近：某些属性,周围的人都具备,用户大概率也具备，例如大学区域。
	行为相似：通过协同过滤,找到行为相似的目标用户，对用户进行分群。

## 用户画像用于做什么
1. 从现有用户中找到我们真正的用户
	真正的用户（高留存. 核心行为频次、完成率高）
2. 找到「真正的用户」的特征
比如卖书的平台，假如是二手书的交易
例:通过他们买卖的书籍，
倒推他们的年龄、受教育程度、地域、消费能力。
从哪儿来
例:通过电话访谈等方式,发现很多来自朋友推荐。
例如，本科，18-30 岁，一线城市，朋友推荐


# 思考问题的方法
## 归因查找
找出事件发生的「主要原因」

1. 末次归因转化路径短,且事件间关联性强的场景（添加好友是因为什么）比如交友软件，通过漂流瓶，附件的人，摇一摇，或者随机推荐好友
2. 递减归因转化路径长,非目标事件差异不大,没有完全主导的假如是一个时间管理工具，某一个买了 VIP 没有广告了，不能看这一刻和刚刚用的功能有什么关系，我们可以归为 6,3,1 比例
3. 首次归因强流量依赖的业务场景,拉人比后续所有事都重要

## 路径挖掘
因为所有的行为都是漏斗过程，路径挖掘分析法的运作原理：逐级展开某一事件的前一级（后一级）事件，观察其流向。


## 行为序列
上面我们聊了路径挖掘分析法，大家可能会说，我们有了路径挖掘模型之后，单个用户的行为序列还有什么意义呢，其实单个用户的行为序列能让我们回归具体的业务场景，发现隐藏在统计数据下被统计数据抹平了细节的更真实的业务场景。其实，路径挖掘分析法有它的局限性，它只是把一群人放到这个路径里进行分析，它反应的是一群人的趋势，但是对于单个用户来讲，趋势肯定是不一样的，所以这时我们就需要运用到行为序列分析法（追求个性化）。行为序列分析法的运作原理：将单个用户的所有行为以时间线的形式进行排列。


# 工具
这边只列出常使用的，并不进行具体介绍。

1. excel
2. Tableau
3. PowerBI
4. jupyter
5. matplotlib
6. numpy
7. pandas

前三个数据分析师要学，后面的不仅仅数据分析师需要，人工智能也需要相关的了解
## excel
略

## Tableau
略
## Power Query
略

## jupyter
### 安装
首先下载的话就需要管理员，管理员权限打开cmd，然后：

```python
pip install jupyter
```

如果没有的话，就需要自己去配置环境变量:把那个jupyter所在的python的scrip啥的路径添加到环境变量下


### 启动
安装完毕后输入jupyter notebook 启动（在git bash中启动可以得到中文jupyter）

基本上的操作都是傻瓜式操作，这边就不特殊介绍，不过需要关注的是，我们要到项目文件下，或者你的测试文件夹下打开对应的git bash，然后输入jupyter notebook，然后创建jupyter，需要我们提前选择好想要打开的文件夹，或者说，提前准备好的位置。


![[image/93ddc73452f2724e61912fe69b473e88_MD5.png]]
### 连接pycharm进行启动
直接创建ipynb文件运行即可，但是需要注意的是，如果pycharm不兼容，就需要手动配置服务器地址，现在对应资源路径运行jupyter notebook，然后复制对应的网址，粘贴进去

![[image/d18308fd0c3e7bb93f942ba9976c63b3_MD5.png]]
![[image/68ed9450d2a701981d6e7f7a8ef54d76_MD5.png]]

## matplotlib
参考教程：[链接](https://matplotlib.org/stable/tutorials/pyplot.html)
### 安装

```python
pip install matplotlib
```

### 折线图

```python
#折线图的绘制
x = range(1,8) # x轴的位置
y = [17, 17, 18, 15, 11, 11, 13]
# 传入x和y, 通过plot画折线图
plt.plot(x,y)
plt.show()
```
![[image/422870b0a4d4b5d75e06d65568ab9fc0_MD5.png]]

```python
x = range(1,8) # x轴的位置
y = [17, 17, 18, 15, 11, 11, 13]
# 传入x和y, 通过plot画折线图
plt.plot(x,y,color='red',alpha=0.5,linestyle='-',linewidth=3)
plt.show()
# '''基础属性设置
# color='red' : 折线的颜色alpha=0.5	: 折线的透明度(0-1) linestyle='--' : 折线的样式linewidth=3		: 折线的宽度—粗细
# '''
# '''线的样式
# -	实线(solid)
# --	短线(dashed)
# -.	短点相间线(dashdot)
# ：	虚点线(dotted)
# '''
```
![[image/294d0d2e28955a14ed047010af7098f2_MD5.png]]

```python
# 折点样式选择
x = range(1,8) # x轴的位置
y = [17, 17, 18, 15, 11, 11, 13]
# 传入x和y, 通过plot画折线图
plt.plot(x, y, marker='>')
plt.show()
```
![[image/02cbd8e8d2e0ac67f9d1d783a3527cd8_MD5.png]]

```python
# 保存图形
import random
x = range(2,26,2) # x轴的位置
y = [random.randint(15, 30) for i in x]

# 设置图片的大小
'''
figsize:指定figure的宽和高，单位为英寸；                                                                                                  dpi参数指定绘图对象的分辨率，即每英寸多少个像素，缺省值为80，1英寸等于2.5cm,A4纸是 21*30cm的纸张
'''
# 设置画布对象，figsize中对应的单位是英寸，dpi是每英寸有多少像素点
plt.figure(figsize=(20,8),dpi=80)

plt.plot(x,y)	# 传入x和y, 通过plot画图
#plt.show()
#  保存(注意：  要放在绘制的下面,并且plt.show()会释放figure资源，如果在显示图像之后保存图片将只能保存空图片。)
# plt.savefig('./t1.png')
#  图片的格式也可以保存为svg这种矢量图格式，这种矢量图放在网页中放大后不会有锯齿#
plt.savefig('./t1.svg')
```
![[image/834e138a130d771419d267e9d8d6e09b_MD5.png]]

```python
# 设置刻度
x = range(2,26,2) # x轴的位置
y = [random.randint(15, 30) for i in x]
plt.figure(figsize=(20,8),dpi=80)

# # 设置x轴的刻度
# plt.xticks(range(1,25))
# # 设置y轴的刻度
# plt.yticks(range(min(y),max(y)+1))

# 构造x轴刻度标签
x_ticks_label = ["{}:00".format(i) for i in x] #rotation = 45 让字旋转45度
plt.xticks(x,x_ticks_label,rotation = 45)
# 设置y轴的刻度标签
y_ticks_label = ["{}℃".format(i) for i in range(min(y),max(y)+1)]
plt.yticks(range(min(y),max(y)+1),y_ticks_label)

plt.plot(x,y)
plt.show()
```
![[image/b87686874b9e0dbd1814fe8e2e8ad0fc_MD5.png]]

```python
import random


x = range(0,120)
y = [random.randint(10,30) for i in range(120)]

plt.figure(figsize=(20,8),dpi=80)

#rotation将字体旋转45度
from matplotlib import font_manager
my_font = font_manager.FontProperties(fname='C:\Windows\Fonts\STSONG.TTF',size=18)
plt.xlabel('时间',rotation=45,fontproperties = my_font)
plt.ylabel("次数",fontproperties=my_font)
# 设置标题
plt.title('每分钟跳动次数',color='red',fontproperties = my_font)

plt.plot(x,y)
```
![[image/d6a23526b5035c9ecba8129a7a163cc7_MD5.png]]

```python
# 一图多线
# 假设大家在30岁的时候，根据自己的实际情况，统计出来你和你同事各自从11岁到30岁每年交的男/女朋友的数量如列表y1和
# y2，请在一个图中绘制出该数据的折线图，从而分析每年交朋友的数量走势。
y1 = [1, 0, 1, 1, 2, 4, 3, 4, 4, 5, 6, 5, 4, 3, 3, 1, 1, 1, 1, 1]
y2 = [1, 0, 3, 1, 2, 2, 3, 4, 3, 2, 1, 2, 1, 1, 1, 1, 1, 1, 1, 1]

x = range( 11, 31 )  # # 设置图形
plt.figure( figsize=(20, 8), dpi=80 )
'''
添加图例:label 对线的解释，然后用plt.legend添加到图片上;
添加颜色: color='red'
线条风格： linestyle='-';   - 实线 、 -- 虚线，破折线、 -. 点划线、 : 点虚线，虚线、 '' 留空或空格线条粗细： linewidth = 5
透明度：   alpha=0.5
'''
plt.plot( x, y1, color='red', label='自己' )
plt.plot( x, y2, color='blue', label='同事' )
# 设置x轴刻度
xtick_labels = ['{}岁'.format( i ) for i in x]
my_font = font_manager.FontProperties( fname='C:\Windows\Fonts\STSONG.TTF', size=18 )
plt.xticks( x, xtick_labels, fontproperties=my_font, rotation=45 )
# 绘制网格（网格也是可以设置线的样式)
# alpha=0.4 设置透明度
plt.grid( alpha=0.4 )

#  添加图例(注意：只有在这里需要添加prop参数是显示中文，其他的都用fontproperties) # 设置位置loc : upper left、 lower left、 center left、 upper center
plt.legend( prop=my_font, loc='upper left' )

# 展示
plt.show()
```
![[image/57cc1ead04a16ac31ff61d1a64f98302_MD5.png]]

### 划分区域
建议使用第二种：

第一种：
```python
import numpy as np
x  =  np.arange(1, 100) #划分子图
fig,axes=plt.subplots(2,2)
ax1=axes[0,0]
ax2=axes[0,1]
ax3=axes[1,0]
ax4=axes[1,1]

fig=plt.figure(figsize=(20,10),dpi=80) #作图1
ax1.plot(x, x) # 作 图 2
ax2.plot(x, -x) #作图3
ax3.plot(x, x**2)
# ax3.grid(color='r', linestyle='--', linewidth=1,alpha=0.3) #作图4
ax4.plot(x, np.log(x))
plt.show()
```
![[image/5e177326d743deaf50580fb95fa31017_MD5.png]]

第二种：

```python
x = np.arange(1,100)
#新建figure对象
fig=plt.figure(figsize=(20,10),dpi=80) #新建子图1
ax1=fig.add_subplot(2,2,1)  ## 2，2是分割成2 * 2  然后画在第一张图上
ax1.plot(x, x)
#新建子图2
ax3=fig.add_subplot(2,2,2)
ax3.plot(x, x**2)
ax3.grid(color='r', linestyle='--', linewidth=1,alpha=0.3)
# 新 建 子 图 3
ax4=fig.add_subplot(2,2,3)
ax4.plot(x, np.log(x))

# 新 建 子 图 4
ax2=fig.add_subplot(2,2,4)
ax2.plot(x, -x)
plt.show()
```

![[image/fbbed4548cbbc778122d78a9458eb281_MD5.png]]

### 散点图

```python
# 散点图
y = [11, 17, 16, 11, 12, 11, 12, 6, 6, 7, 8, 9, 12, 15, 14, 17, 18, 21, 16, 17, 20, 14, 15, 15, 15, 19, 21, 22, 22, 22,
     23]
x = range( 1, 32 )

# 设置图形大小
plt.figure( figsize=(20, 8), dpi=80 )
# 使用scatter绘制散点图
plt.scatter( x, y, label='3月份' )
# 调整x轴的刻度
my_font = font_manager.FontProperties( fname='C:\Windows\Fonts\STSONG.TTF', size=10 )

_xticks_labels = ['3月{}日'.format(i) for i in x]

plt.xticks( x[::3], _xticks_labels[::3], fontproperties=my_font,rotation=45)
plt.xlabel( ' 日 期 ', fontproperties=my_font )
plt.ylabel( '温度', fontproperties=my_font )
# 图 例
plt.legend( prop=my_font )
plt.show()
```
![[image/7427a56525f3dc2df50a7864efb1c9bc_MD5.png]]

### 条形图
```python
#条形图
my_font =  font_manager.FontProperties(fname='C:\Windows\Fonts\STSONG.TTF',size=16)
a = ['流浪地球','疯狂的外星人','飞驰人生','大黄蜂','熊出没·原始时代','新喜剧之王']
b = [38.13,19.85,14.89,11.36,6.47,5.93]

plt.figure(figsize=(20,8),dpi=80)

# 绘制条形图的方法
'''
width=0.3  条形的宽度
'''
rects = plt.bar(range(len(a)),b,width=0.3,color='r')
plt.xticks(range(len(a)),a,fontproperties=my_font,rotation=45)
# 在条形图上加标注(水平居中)
for rect in rects:
    height = rect.get_height()
    plt.text(rect.get_x() + rect.get_width() / 2, height+0.3, str(height),ha="center")
plt.show()
```
![[image/934e6959a0fc71b8f4626cbb8ff158fc_MD5.png]]
### 直方图

```python
'''
现有250部电影的时长，希望统计出这些电影时长的分布状态(比如时长为100分钟到120分钟电影的数量，出现的频率)等信息，你应该如何呈现这些数据？
'''
# 1）准备数据
time = [131,   98,    125,   131,   124,   139,   131, 117, 128, 108, 135, 138, 131, 102, 107, 114,
119,   128,   121,   142,   127,   130,   124, 101, 110, 116, 117, 110, 128, 128, 115,   99,
136,   126,   134,   95,    138,   117,   111,78, 132, 124, 113, 150, 110, 117,  86,    95, 144,
105, 126, 130,126, 130, 126, 116, 123, 106, 112, 138, 123, 86, 101,   99, 136,123,
117,   119,   105,   137, 123, 128, 125, 104, 109, 134, 125, 127,105, 120,  107,   129, 116,
108,   132,   103,   136, 118, 102, 120, 114,105, 115, 132, 145, 119, 121,  112,   139, 125,
138,   109,   132,   134,156, 106, 117, 127, 144, 139, 139, 119, 140,   83,    110,   102,123,
107,   143,   115,   136, 118, 139, 123, 112, 118, 125, 109, 119, 133,112,  114,   122, 109,
106,   123,   116,   131,   127, 115, 118, 112, 135,115,   146,   137,   116,   103,   144,   83,    123,
111,   110,   111,   100,   154,136, 100, 118, 119, 133,   134,   106,   129,   126,   110,   111,   109,
141,120, 117, 106, 149, 122, 122, 110, 118, 127, 121, 114, 125, 126,114, 140, 103,
130,   141, 117, 106, 114, 121, 114, 133, 137,    92,121,    112,   146,   97,    137, 105,  98,
117,   112,   81,    97, 139, 113,134, 106, 144, 110, 137,  137,   111,   104,   117, 100, 111,
101,   110,105, 129, 137, 112, 120, 113, 133, 112,    83,    94,    146,   133,   101,131, 116,
111,   84, 137, 115, 122, 106, 144, 109, 123, 116, 111,111, 133, 150]

from matplotlib import font_manager
from matplotlib import pyplot as plt
my_font =  font_manager.FontProperties(fname='C:\Windows\Fonts\STSONG.TTF',size=18) # 2）创建画布
plt.figure(figsize=(20, 8), dpi=100) # 3）绘制直方图

# 设置组距
distance = 1
# 计算组数
group_num = int((max(time) - min(time)) / distance) # 最大值减去最小值除以组距就得到组数
plt.hist(time, bins=group_num) #bins是组数

# 修改x轴刻度显示
plt.xticks(range(min(time), max(time))[::2])

# 添加网格显示
plt.grid(linestyle="--", alpha=0.5)

#添 加 x,  y 轴 描 述 信 息
plt.xlabel("电影时长大小",fontproperties=my_font)
plt.ylabel("电影的数据量",fontproperties=my_font)
# 4）显示图像
plt.show()
```
![[image/5209b4817a5b365caf09b38ff2cfc44f_MD5.png]]

### 饼状图

```python
import matplotlib.pyplot as plt
import matplotlib
label_list = ["第一部分", "第二部分", "第三部分"]  # 各部分标签
size = [55, 35, 10]    # 各部分大小
color = ["red", "green", "blue"]   # 各部分颜色
explode = [0, 0.05, 0] # 各部分突出值
"""
绘 制 饼 图
explode： 设 置 各 部 分 突 出
label: 设 置 各 部 分 标 签
labeldistance:设置标签文本距圆心位置，1.1表示1.1倍半径
autopct： 设 置 圆 里 面 文 本
shadow：设置是否有阴影
startangle：起始角度，默认从0开始逆时针转
pctdistance：设置圆内文本距圆心距离返回值
l_text：圆内部文本，
matplotlib.text.Text object
p_text：圆外部文本"""
matplotlib.rcParams['font.sans-serif']=['SimHei']  #为了显示汉字
patches, l_text, p_text = plt.pie(size,explode=explode, colors=color, labels=label_list,
                                  labeldistance=1.1, autopct="%1.1f%%", shadow=True, startangle=90, pctdistance=0.6)
plt.axis("equal")  # 设置横轴和纵轴大小相等，这样饼才是圆的
plt.legend()
plt.show()
```

![[image/8d798c7d7197d5a382063611bbdad6dc_MD5.png]]

## numpy

见31 数据分析（中)：[链接](https://blog.csdn.net/Micoreal/article/details/133679349)



## pandas

见32 数据分析（下): [链接](https://blog.csdn.net/Micoreal/article/details/133745366)
























