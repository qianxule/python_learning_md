@[TOC]
# 时间复杂度和空间复杂度
了解408那些就行

# 排序算法
针对排序算法，掌握通常的 8 种排序算法即可，依次是 冒泡，选择，插入，希尔，快排，堆排，归并，计排。

对于排序算法分为以下 5 类：
1. 插入类：插入排序，希尔排序
2. 选择类：选择排序，堆排序
3. 交换类：冒泡排序，快速排序
4. 归并类：归并排序
5. 分配类：基数排序(计数排序，桶排序)，通过用额外的空间来”分配”和”收集”来实现排序，它们的时间复杂度可达到线性阶：O(n)
实际用的最多的是快速排序，堆排序，还有计数排序，因为在下面的排序算法中，大家要对这三种排序算法非常熟练！

![[image/7b4346e16ba19602318c236afc1efce2_MD5.png]]
## 冒泡排序
通过对待排序序列从前向后（从下标较小的元素开始）,依次对相邻两个元素的值进行两两比较，若发现逆序则交换，使值较大的元素逐渐从前移向后部，就如果水底下的气泡一样逐渐向上冒。

```python
def bubble(self):
	arr = self.arr
	i = 9
	while i > 0:
		j = 0
		while j < i:
			if arr[j] > arr[j + 1]:
				arr[j], arr[j + 1] = arr[j + 1], arr[j]
			j += 1
		i -= 
```

## 选择排序
上面的冒泡排序在内层比较时，我们发现较大的数就交换，会造成交换的频次过高，如果我们通过一个变量记录时刻记录最大值，一轮比较下来，将最大值再和最后的元素交换，这样一轮比较就只需要交换一次。选择排序正是使用这种思想对冒泡进行了改进，下面的代码依然是实现从小到大排序，外层循环依然记录的是无序数的情况，但是这次我们每轮遍历找到最小值，让最小值和最前面的元素发生交换，循环进行，最终数组有序。

```python
def select(self):
	arr = self.arr
	i = 0
	while i < len(arr) - 1:
		min_pos = i
		j = i + 1
		while j < len(arr):
			if arr[j] < arr[min_pos]:
				min_pos = j
			j += 1
		# 开始交换
		arr[i], arr[min_pos] = arr[min_pos], arr[i]
		i += 1
```
## 插入排序
插入排序的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。插入排序在实现上，通常采用in-place排序（即只需用到O(1)的额外空间的排序），因而在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为最新元素提供插入空间。

```python
def insert(self):
	arr = self.arr
	i = 1
	while i < len(arr):
		j = i - 1
		insert_value = arr[i]
		while j >= 0:
			if insert_value < arr[j]:
				arr[j + 1] = arr[j]
			else:
				break
			j -= 1
		arr[j + 1] = insert_value
		i += 1

def insert(nums):
	for i in range(0, len(nums)):
		for j in range(i, 0, -1): # 反向遍历目标值到第二个位置，满足目标值小于它下一个值，交换两个值
			if nums[j] < nums[j - 1]:
				nums[j], nums[j - 1] = nums[j - 1], nums[j]
			else:
				break
```

## 希尔排序
相对于插入排序，希尔排序增加了一层循环，也就是步长gap，然后内层的循环中，j原有跟1有关的都改为gap，同时i每次以gap位置作为插入的起始点，步长gap一开始是数组的长度除2，也就是5，后面每次以除2进行递减，直到gap为1，当gap等于1时，内层就是原有的插入排序。

```python
def shell(self):
	arr = self.arr
	# 最外层循环控制步长
	gap = self.arrlen >> 1
	while gap > 0:
		i = gap # 第一个要插入的数的位置
		while i < self.arrlen:
			insert_val = arr[i]
			j = i - gap # 有序序列的最后一个位置
			while j >= 0:
				if insert_val < arr[j]:
					arr[j + gap] = arr[j]
				else:
					break
				j -= gap
			arr[j + gap] = insert_val # 一定要把赋值放到 break 后
			i += 1 #只有这一个地方不是 gap
			gap >>= 1 
```

## 快速排序
快排的核心思想是分治，首先我们的目标依然是从小到大排序，我们找到数组中一个分割值，把比分割值小的数都放在数组的左边，把分割值大的数放在数组的右边，这样分割值的位置就确定下来了，数组一分为二，我们只需要排前一半数组，后一半数组，复杂度直接减半，通过这种思想，不断的进行递归，最终分割的只剩余一个元素，就自然有序。

arr_quick是我们的快排的递归函数，关键是partition，也就是分割函数的编写，分割函数返回分割点的下标值，这样我们通过再次递归，arr_quick数组的前半部分，然后arr_quick数组的后半部分，递归编写时注意到对返回的分割点的下标减一和加一，递归的结束条件，当left等于right时，说明被分割后的数组只剩余一个元素，这时递归结束。

```python
def partition(self, left, right):
# 注意这里使用的是快排的替换法，与一般的快排不一样，也可以使用正常的交换法进行替代
	arr = self.arr
	# k 始终指向比分割值小的数要放置的位置的下标
	k = left
	for i in range(left, right):
		# 如果元素小于分割值，就交换 k指向的是要交换的位置
		if arr[i] < arr[right]:
			arr[i], arr[k] = arr[k], arr[i]
			k += 1
	arr[k], arr[right] = arr[right], arr[k]
	return k
	
def quick(self, left, right):
	if left < right:
		# pivot 是分割值的下标
		pivot = self.partition(left, right)
		self.quick(left, pivot - 1)
		self.quick(pivot + 1, right)
```

## 堆排序

如果排序的数据较多，而且又要求空间复杂度为O(1)，那么我们会使用堆排，因为相对于快排，堆排的时间复杂度是最坏和平均都是O(nlogn)。

堆（英语：Heap）是计算机科学中的一种特别的树状数据结构。若是满足以下特性，即可称为堆：“给定堆中任意节点 P 和 C，若 P 是 C 的母节点，那么 P 的值会小于等于（或大于等于）C 的值”。若母节点的值恒小于等于子节点的值，此堆称为最小堆（min heap）；反之，若母节点的值恒大于等于子节点的值，此堆称为最大堆（max heap）。在堆中最顶端的那一个节点，称作根节点（root node），根节点本身没有母节点（parent node）。

```python
# 调整某一棵子树为大根堆
def adjust_max_heap(self, adjust_pos, arr_length):
	arr = self.arr
	dad = adjust_pos
	son = 2 * dad + 1 # 左孩子和父亲的下标位置关系
	while son < arr_length: # 下标要小于列表的长度
		if son + 1 < arr_length and arr[son] < arr[son + 1]:
			son += 1
		if arr[son] > arr[dad]:
			arr[son], arr[dad] = arr[dad], arr[son]
			dad = son
			son = 2 * dad + 1
		else:
			break

def heap(self):
	arr = self.arr
	# 调整为大根堆
	for i in range(self.len // 2 - 1, -1, -1):
		self.adjust_max_heap(i, self.len)
	arr[0], arr[self.len - 1] = arr[self.len - 1], arr[0] #交换顶部元素和最后一个元素
	for i in range(self.len - 1, 1, -1): # 这里是不断控制无序数的总长度
		self.adjust_max_heap(0, i)
		arr[0], arr[i - 1] = arr[i - 1], arr[0]
```

## 归并排序
归并排序的时间复杂度是O(nlogn)，但是空间复杂度是O(n),因此正常==单机==排序时，优先使用堆排，所以归并面试的比较少，但是一旦==多机==排序，就会用到归并排序的思想，归并排序下面的代码是采用递归方式实现的，非递归的归并排序难度较高，面试时只需完成下面的递归的二路归并即可。

通过下面代码可以看出编写归并排序的核心是==合并两个有序数组==

当一台电脑的内存不足以放置所有的数据时，如果这时不采用外部排序手法，那么多机同时排序提高效率是常用的办法，多机排序后的结果最终仍需以归并排序的思想进行合并。

```python
def merge(self, low, mid, high):
	arr = self.arr
	arr_t = self.arr1
	for i in range(low, high + 1):
		arr_t[i] = arr[i]
	i = low
	j = mid + 1
	k = low
	# 合并两个有序数组
	while i <= mid and j <= high:
		if arr_t[i] < arr_t[j]:
			arr[k] = arr_t[i]
			i += 1
		else:
			arr[k] = arr_t[j]
			j += 1
		k += 1
	while j <= high:
		arr[k] = arr_t[j]
		k += 1
		j += 1
	while i <= mid:
		arr[k] = arr_t[i]
		k += 1
		i += 1
# 归并排序不限制是二路归并（也可以叫两两归并），
# 还是多个归并，因为两两归并编写简单，同时面试官
# 也不会要求编写其他数目的归并，所以大家掌握两两归并即可
def merge_sort(self, low, high):
	# 递归分割
	if low < high:
		mid = (low + high) // 2
		self.merge_sort(low, mid)
		self.merge_sort(mid + 1, high)
		self.merge(low, mid, high)
```

## 计数排序
上面的快排堆排在排序1亿个数时，需要的时间在38秒，当然这是我的电脑的测试结果，你的电脑配置更好，自然会比我的更快一些，但是30多秒在工作中很多时候是我们无法接受的，有没有更好的办法呢？答案是有的，计数排序就是用空间换时间的方式来提高排序效率，如果我们用计数排序排我们随机的0-99的数，总计1亿个，排序的时间不到1秒钟，为什么会这么快，因为计数排序的复杂度是O(n+m)，m就是下面代码中的大M，数的数值范围，因为数的变化返回是0-99，所以计数排序的执行次数是1亿+1百的数量级，但是堆排是O(nlogn), 得到的是27亿次，实际应用中，商品的价格，销量，人的年龄,身高，都是在一个有限的范围内，因此为了提高排序效率，使用计数排序是很常见的。

```python
def count_sort(self):
	arr = self.arr
	count = [0] * self.value_range
	# 遍历 arr，统计 0-99 出现的次数
	for i in arr:
		count[i] += 1
	k = 0
	i = 0
	#通过出现的次数，给原数组去填写值
	while i < self.value_range:
		for j in range(count[i]):
			arr[k] = i
			k += 1
		i += 1
```

分配类的排序包含计数排序，基数排序，还有桶排序，有与基数排序是按照数据的每一位进行比较，编写相对复杂一些，因此面试较少，假如数的范围较大，比如是 0 到 1 千万，桶排序的思想是划分若干个区间，比如划分 10 个区间，这时遍历一边数组，将 0 到 100 万的放入第一个桶，100 万至 200 万的放入第二个桶，依次类推，最后 900 万到 1 千万，放到最后一个桶，这样对每个桶分别进行排序，然后再合并起来即可。
## 补充
1. N 个无序数的数组，让找出第 K 大的数？

如果这时你回答对数组排序，那么这里面试就挂了，之前做过找第一大，和第二大的数，只需遍历一次数组即可，这里也是这样的，首先我们对前 K 个数，建立小根堆，为什么要是小根堆，因为对于 K 个元素，小根堆的根部就是第 K 大的元素，这时我们拿 K+1 个元素，依次跟堆顶进行比较，如果大于堆顶，就移除堆顶，放入新元素，重新调整为小根堆，直到遍历到数组末尾，堆顶即为第 K 大的元素，这样的复杂度只有 O（NlogK）,比排序复杂度降低了很多。

2. 如果内存无法存储 K 个元素怎么办？

那实际上就是问取n-k小的数据，逆向思维一下。

3. 刚才找的第 K 大的元素是没有考虑重复元素的问题，现在要找的第 K 大是不含有重复元素的第 K 大？

虽然我们可以讲建立小根堆时，重复的元素不放入，这是一种解决方法，但是面试官一旦问的是去重问题，我们首先要回答的是位图，因为位图是最快的去重方法（采用空间换时间）

PS:这边简要介绍一下==位图==（数据结构）

实际上就是判断这个数据是否出现过，形式和count[]这种数组类似，不过为了节省空间，就采用每一bit进行判断，比如出现过1，就在对应的bit上改0为1，出现过4就在对应的bit上改0为1，最后用这个常数来进行判断元素的出现情况。最后的效果相比于数组来储存来说就是使用一个整型数字就可以统计完全这个数组的值。

详情见[链接](https://blog.csdn.net/qq_56444564/article/details/128404586)

这边给出自己写的一个位图的python代码

```python
class BitMap:
	def __init__(self):
		self.bitmap = 0//此数据就是位图的核心
		self.arr = [9,20,13,24,13]//随便一个数组，等等拿来去重用的
		self.new_arr = []
	
	def duplicate_remove(self):
		for i in self.arr:
			if self.bitmap & 1<<i :
				pass
			else:
				self.bitmap |= 1<<i
				self.new_arr.append(i)		
```

# 查找
## 二分查找
针对有序数组，我们经常使用的是二分查找。

```python
def bsearch(arr,target):
	low = 0
	high = len(arr) - 1
	while low <= high:
		mid=(low+high)//2 # 由于python不会溢出，所以这里可以这么写
		if target<arr[mid]:
			high = mid - 1
		elif target>arr[mid]:
			low = mid + 1
		else:
			return mid
	return -1
```

## 哈希查找
二分查找的时间复杂度是 O(log n)，但是在一些对查找性能要求极高的场景无法满足要求，比如淘宝的负载均衡服务器，如果我们需要 O(1)时间复杂度的查找，就必须使用哈希，哈希又叫散列，散列表（Hash table，也叫哈希表），是根据键（Key）而直接访问在内存存储位置的数据结构。也就是说，它通过计算一个关于键值的函数，将所需查询的数据映射到表中一个位置来访问记录，这加快了查找速度。这个映射函数称做散列函数，存放记录的数组称做散列表.

散列函数能使对一个数据序列的访问过程更加迅速有效，通过散列函数，数据元素将被更快地定位。

实际工作中需视不同的情况采用不同的哈希函数，通常考虑的因素有：
· 计算哈希函数所需时间
· 关键字的长度
· 哈希表的大小
· 关键字的分布情况
· 记录的查找频率

```python
# 比较有名的哈希函数 elf哈希函数，这个实际上也不用记忆，只需要知道就行
def elf_hash(hash_str):
	h = 0
	g = 0
	for i in hash_str:
		h = (h << 4) + ord(i)# ord函数实际上就是取出i的acill值
		g = h & 0xf0000000
		if g:
			h ^= g >> 24
		h &= ~g
	return h % MAXKEY
# 构造相关的哈希表
def use_hash():
	str_list=["xiongda","lele","hanmeimei","wangdao","fenghua"]
	hash_table=[None]*MAXKEY
	for i in str_list:
		hash_table[elf_hash(i)]=i
```

当数据量较大时，会发生哈希冲突，对不同的关键字可能得到同一散列地址，即 k1≠k2，而 f(k1)=f(k2)，这种现象称为哈希冲突（英语：Collision），解决哈希冲突有以下几种方法：

1. 开放寻址法：Hi=(H(key) + di) MOD m,i=1,2，…，k(k<=m-1），其中 H(key）为散列函数，m 为散列表长，di 为增量序列，可有下列三种取法：
1.1. di=1,2,3，…，m-1，称线性探测再散列；
1.2. di=1^2,-1^2,2^2,-2^2，⑶^2，…，±（k)^2,(k<=m/2）称二次探测再散列；
1.3. di=伪随机数序列，称伪随机探测再散列。

2. 再散列法：Hi=RHi(key),i=1,2，…，k RHi 均是不同的散列函数，即在同义词产生地址冲突时计算另一个散列函数地址，直到冲突不再发生，这种方法不易产生“聚集”，但增加了计算时间。
3. 单独链表法：将散列到同一个存储位置的所有元素保存在一个链表中。除了二分查找，哈希查找，针对我们上面讲的二叉树，红黑树，可以进行二叉树，红黑树查找，红黑树的查找依然是 O(log2n)的时间复杂度，相对于二分查找的好处是其新增和删除操作次数少，其实还有 B 树查找。
# sort常用补充，场景介绍
sort 函数是 list 列表中的函数，而 sorted 可以对 list 或者 iterator 进行排序

sort 和 sorted 的比较：
用 sort 函数对列表排序时会影响列表本身，而 sorted 不会，他会返回出一个新的列表。

sorted(iterable,key，reverse）
参数：
iterable 可以是 list 或者 iterator
key 是带一个参数的函数
reverse 为 False 或者 True

实际用法：
sorted(iterable[[, key[, reverse]]])
s.sort([[, key[, reverse]]])

用法：

```python
class Student:
	def __init__(self, name, grade, age):
		self.name = name
		self.grade = grade
		self.age = age
	# repr类似与str 实际上就是你在返回这个对象的时候，返回的是你自定义的类型，比如下文就是对应的元组类型，如果采用str，返回的就一定需要是str类型
	def __repr__(self):
		return repr((self.name, self.grade, self.age))
student_objects = [
	Student('john', 'A', 15),
	Student('jane', 'B', 12),
	Student('dave', 'B', 10),
]
# 对于python的lambda函数来说 他的格式是从student_objects中迭代取出元素 为student，然后排序student.age进行排序
sorted(student_objects, key=lambda student: student.age) # sort by age

输出：
[('dave', 'B', 10), ('jane', 'B', 12), ), ('john', 'A', 15)]
```


相同的也可以调用库函数

```python
operator 库函数自定义排序（ Operator Module Functions）
前面我们看到的利用 key-function 来自定义排序，同时 Python 也可以通过
operator 库来自定义排序，而且通常这种方法更好理解并且效率更高。
operator 库提供了 itemgetter(), attrgetter(), and a methodcaller()三个函数

>>> from operator import itemgetter, attrgetter
>>> sorted(student_tuples, key=itemgetter(2))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]
>>> sorted(student_objects, key=attrgetter('age'))
[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]

同时还支持多层排序
>>> sorted(student_tuples, key=itemgetter(1,2))
[('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]
>>> sorted(student_objects, key=attrgetter('grade', 'age'))
[('john', 'A', 15), ('dave', 'B', 10), ('jane', 'B', 12)]
```

