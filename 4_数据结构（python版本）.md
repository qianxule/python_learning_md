@[TOC]

默认是学过基础的408的数据结构的或者对于基础的有所了解。
# 栈
栈又名堆栈，它是一种运算受限的线性表。其限制是仅允许在表的一端进行插入和删除运算，这一端被称为栈顶，相对地，把另一端称为栈底。向一个栈插入新元素又称作进栈、入栈或压栈，它是把新元素放到栈顶元素的上面，使之成为新的栈顶元素；从一个栈删除元素又称作出栈或退栈，它是把栈顶元素删除掉，使其相邻的元素成为新的栈顶元素。由于堆栈数据结构只允许在一端进行操作，因而按照后进先出（LIFO, LastIn First Out）的原理运作。

栈可以用==数组==实现，也可以用==链表==实现，这里我们将通过链表结构来实现栈，这里我们可以通过链表的头部插入法，头部删除法，实现先进后出的效果。

```python
class Stack:
	def __init__(self):
		self.stack = []
	
	def push(self, ele):
		self.stack.append(ele)
	def pop(self):
		return self.stack.pop()
	def top(self):
		if self.empty():
			return "stack is empty"
		return self.stack[-1]

	def empty(self):
		return self.stack == []
	def size(self):
		return len(self.stack)

if __name__ == '__main__':
	stack = Stack()
	stack.push(1)
	stack.push(2)
	stack.push(3)
	print(stack.stack)
	stack.pop()
	stack.pop()
	print(stack.stack)
```

# 队列
队列：是==先进先出（FIFO, First-In-First-Out）==的线性表。在具体应用中通常用链表或者数组来实现。队列只允许在后端（称为 rear）进行插入操作，在前端（称为 front）进行删除操作。通过对链表进行头部删除，尾部插入，实现每个元素达到先进先出的效果。

代码实际上和链表相类似，实际上就只需要修改其pop的方式即可。

下面的稍微理解一下就行
```python
from collections import deque
#增删查改
queue = deque(["Eric", "John", "Michael"])
queue.append('luke')
print(queue)
print(queue.popleft())
print(queue)
queue[0] ='xiongda'
print(queue[0])

```

# 二叉树
在计算机科学中，二叉树是每个节点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）。二叉树常被用于实现二叉查找树和二叉堆。二叉树的每个节点至多只有二棵子树(不存在度大于 2 的节点)，二叉树的子树有左右之分，次序不能颠倒。

二叉树的存储可以是顺序存储（顺序存储在堆排序中进行学习），也可以是链式存储，下面我们来看通过链式存储来实现一颗二叉树存储。

```python
# coding=utf-8
class Node(object):
"""节点类"""
	def __init__(self, elem=-1, lchild=None, rchild=None):
		self.elem = elem
		self.lchild = lchild
		self.rchild = rchild

class Tree(object):
"""树类"""
def __init__(self):
	self.root = Node()
	self.myQueue = []
	
def add(self, elem):
"""为树添加节点"""
	node = Node(elem)
	if self.root.elem == -1: # 如果树是空的，则对根节点赋值
		self.root = node
		self.myQueue.append(self.root)
	else:
		treeNode = self.myQueue[0] # 此结点的子树还没有齐。
	if treeNode.lchild == None:
		treeNode.lchild = node
		self.myQueue.append(treeNode.lchild)
	else:
		treeNode.rchild = node
		self.myQueue.append(treeNode.rchild)
		self.myQueue.pop(0) # 如果该结点存在右子树，将此结点丢弃。

	def front_digui(self, root):
	"""利用递归实现树的先序遍历"""
		if root == None:
			return
		print(root.elem, end="")
		self.front_digui(root.lchild)
		self.front_digui(root.rchild)
	def middle_digui(self, root):
	"""利用递归实现树的中序遍历"""
		if root == None:
			return
		self.middle_digui(root.lchild)
		print(root.elem, end="")
		self.middle_digui(root.rchild)
	def later_digui(self, root):
	"""利用递归实现树的后序遍历"""
		if root == None:
			return
		self.later_digui(root.lchild)
		self.later_digui(root.rchild)
		print(root.elem, end="")
	def front_stack(self, root):
	"""利用堆栈实现树的先序遍历"""
		if root == None:
			return
		myStack = []
		node = root
		while node or myStack:
			while node: # 从根节点开始，一直找它的左子树
				print(node.elem, end="")
				myStack.append(node)
				node = node.lchild
			node = myStack.pop() # while 结束表示当前节点 node 为空，即前一个节点没有左子树了
			node = node.rchild # 开始查看它的右子树
	def middle_stack(self, root):
	"""利用堆栈实现树的中序遍历"""
		if root == None:
			return
		myStack = []
		node = root
		while node or myStack:
			while node: # 从根节点开始，一直找它的左子树
				myStack.append(node)
				node = node.lchild
			node = myStack.pop() # while 结束表示当前节点 node 为空，即前一个节点没有左子树了
			print(node.elem, end="")
			node = node.rchild # 开始查看它的右子树
	def later_stack(self, root):
	"""利用堆栈实现树的后序遍历"""
		if root == None:
			return
		myStack1 = []
		myStack2 = []
		node = root
		myStack1.append(node)
		while myStack1: # 这个 while 循环的功能是找出后序遍历的逆序，存在 myStack2 里面
			node = myStack1.pop()
			if node.lchild:
				myStack1.append(node.lchild)
			if node.rchild:
				myStack1.append(node.rchild)
			myStack2.append(node)
		while myStack2: # 将 myStack2 中的元素出栈，即为后序遍历次序
			print(myStack2.pop().elem, end="")
	def level_queue(self, root):
	"""利用队列实现树的层次遍历"""
		if root == None:
			return
		myQueue = []
		node = root
		myQueue.append(node)
		while myQueue:
			node = myQueue.pop(0)
			print(node.elem, end="")
			if node.lchild != None:
				myQueue.append(node.lchild)
			if node.rchild != None:
				myQueue.append(node.rchild)

if __name__ == '__main__':
"""主函数"""
elems = range(10) # 生成十个数据作为树节点
tree = Tree() # 新建一个树对象
for elem in elems:
tree.add(elem) # 逐个添加树的节点
print('队列实现层次遍历:')
tree.level_queue(tree.root)
print('\n\n 递归实现先序遍历:')
tree.front_digui(tree.root)
print('\n 递归实现中序遍历:')
tree.middle_digui(tree.root)
print('\n 递归实现后序遍历:')
tree.later_digui(tree.root)
print('\n\n 堆栈实现先序遍历:')
tree.front_stack(tree.root)
print('\n 堆栈实现中序遍历:')
tree.middle_stack(tree.root)
print('\n 堆栈实现后序遍历:')
tree.later_stack(tree.root)
```

对于二叉树的建树，我们进行一个总结，首先我们需要将每一个字母作为一个二叉树的节点进行存储，所以首先通过循环，为每一个节点申请空间，通过指针数组存储每一个节点对应的指针值。在建树过程中，循环控制要进入树的元素，内部判断找到要添加的位置。

# 红黑树

红黑树（英语：Red–black tree）是一种自平衡二叉查找树，是在计算机科学中用到的一种数据结构，典型的用途是实现关联数组。

在 AVL 树中，任一节点对应的两棵子树的最大高度差为 1，因此它也被称为高度平衡树。查找、插入和删除在平均和最坏情况下的时间复杂度都是O(log2n)。增加和删除元素的操作则可能需要借由一次或多次树旋转，以实现树的重新平衡。

红黑树相对于 AVL 树的时间复杂度是一样的，但是优势是当==插入或者删除==节点时，红黑树实际的调整次数更少，旋转次数更少，因此红黑树插入删除的效率高于 AVL 树，红黑树相对于 AVL 树来说，牺牲了部分平衡性以换取插入/删除操作时少量的旋转操作，整体来说性能要优于 AVL 树。

红黑树是每个节点都带有颜色属性的二叉查找树，颜色为红色或黑色。在二叉查找树强制一般要求以外，对于任何有效的红黑树我们增加了如下的额外要求：
1. 节点是红色或黑色。
2. 根是黑色。
3. 所有叶子都是黑色（叶子是 NIL 节点）。
4. 每个红色节点必须有两个黑色的子节点。（从每个叶子到根的所有路径上不能有两个连续的红色节点。）
5. 从任一节点到其每个叶子的所有简单路径都包含相同数目的黑色节点。

先定义一些基本的操作

```python
# 颜色常量
RED = 0
BLACK = 1

# 左旋操作
def left_rotate(tree, node):
	if not node.right:
		return False
	node_right = node.right
	node_right.p = node.p
	if not node.p:
		tree.root = node_right
	elif node == node.p.left:
		node.p.left = node_right
	else:
		node.p.right = node_right
	if node_right.left:
		node_right.left.p = node
	node.right = node_right.left
	node.p = node_right
	node_right.left = node

# 右旋操作
def right_rotate(tree, node):
	if not node.left:
		return False
	node_left = node.left
	node_left.p = node.p
	if not node.p:
		tree.root = node_left
	elif node == node.p.left:
		node.p.left = node_left
	elif node == node.p.right:
		node.p.right = node_left
	if node_left.right:
		node_left.right.p = node
	node.left = node_left.right
	node.p = node_left
	node_left.right = node

# 替换操作
def transplant(tree, node_u, node_v):
"""用 v 替换 u
:param tree: 树的根节点
:param node_u: 将被替换的节点
:param node_v: 替换后的节点
:return: None
"""
	if not node_u.p:
		tree.root = node_v
	elif node_u == node_u.p.left:
		node_u.p.left = node_v
	elif node_u == node_u.p.right:
		node_u.p.right = node_v
	# 加一下为空的判断
	if node_v:
		node_v.p = node_u.p

# 寻找前驱节点		
def tree_maximum(node):
"""找到以 node 节点为根节点的树的最大值节点 并返回
:param node: 以该节点为根节点的树
:return: 最大值节点
"""
	temp_node = node
	while temp_node.right:
		temp_node = temp_node.right
	return temp_node

# 寻找后继节点
def tree_minimum(node):
"""找到以 node 节点为根节点的树的最小值节点 并返回
:param node: 以该节点为根节点的树
:return: 最小值节点
"""
	temp_node = node
	while temp_node.left:
		temp_node = temp_node.left
	return temp_node

# 前序遍历
def preorder_tree_walk(node):
	if node:
		print(node.value, node.color)
		preorder_tree_walk(node.left)
		preorder_tree_walk(node.right)

# 打印红黑树的相关内容
def rbtree_print(node, key, direction):
	if node:
		if direction == 0: # tree 是根节点
			print("%2d(B) is root" % node.value)
		else: # tree 是分支节点
			print("%2d(%s) is %2d's %6s child" % (node.value, ("B" if node.color == 1 else "R"), key,("right" if direction == 1 else "left")))
			
		rbtree_print(node.left, node.value, -1)
		rbtree_print(node.right, node.value, 1)

```

## 红黑树的插入
具体原理不细讲，这边对于进行一个操作的总结：

首先就是先进行插入，插入的位置根据二叉排序树而定，插入后，再进行下面的颜色调换。

插入的思路（类似二叉排序树的思路）：

```python
先寻找到对应的位置
插入对应元素
```

调换位置的思路：
```python
if(插入的节点是根节点):
	将这个节点涂成黑色
elif(插入的节点父节点是黑色的):
	不需要操作
elif(插入的节点的父节点是红色的):
	if(叔叔节点是红色):
		将祖父 父亲 叔叔 三个节点颜色变成相反的
		将祖父节点变成当前的新插入的节点进行再一次讨论
	elif(叔叔节点是黑色 且 自己是父亲节点的右孩子):
		将父亲节点作为‘当前的节点’
		基于当前节点左旋
		# 这时候实际上就是为了转化成下面的那种情况，然后再走下面那种情况的代码即可
	elif(叔叔节点是黑色 且 自己是父亲节点的左孩子):
		将父亲 祖父节点 俩个节点的颜色设置为相反的
		基于祖父节点进行右旋
```

实际上的代码：

```python
# 定义红黑树节点
class RedBlackTreeNode(object):
	def __init__(self, value):
		self.value = value
		self.left = None
		self.right = None
		self.p = None
		self.color = RED

# 红黑树
class RedBlackTree(object):
def __init__(self):
	self.root = None
	
# 插入节点
def insert(self, node):
	# 找到最接近的节点
	temp_root = self.root
	temp_node = None
	while temp_root:
		temp_node = temp_root
		if node.value == temp_node.value:
			return False
		elif node.value > temp_node.value:
			temp_root = temp_root.right
		else:
			temp_root = temp_root.left
	# 在相应位置插入节点
	if not temp_node:
		self.root = node
		node.color = BLACK
	elif node.value < temp_node.value:
		temp_node.left = node
		node.p = temp_node
	else:
		temp_node.right = node
		node.p = temp_node
	# 调整树
	self.insert_fixup(node)

# 插入数据之后，再调整树的颜色
def insert_fixup(self, node):
	if node.value == self.root.value:
		return
	# 为什么是这个终止条件？
	# 因为如果是这个终止条件那就不需要调整
	while node.p and node.p.color == RED:
	# 只要进入循环则必有祖父节点 否则父节点为根节点 根节点颜色为黑色 不会进入循环
		if node.p == node.p.p.left:
			node_uncle = node.p.p.right
			# 1. 没有叔叔节点 若此节点为父节点的右子 则先左旋再右旋 否则直接右旋
			# 2. 有叔叔节点 叔叔节点颜色为黑色
			# 3. 有叔叔节点 叔叔节点颜色为红色 父节点颜色置黑 叔叔节点颜色置黑 祖父节点颜色置红 continue
			# 注: 1 2 情况可以合为一起讨论 父节点为祖父节点右子情况相同 只需要改指针指向即可
			# 叔叔节点是红色的
			if node_uncle and node_uncle.color == RED:
				node.p.color = BLACK
				node_uncle.color = BLACK
				node.p.p.color = RED
				node = node.p.p
				continue
			# 叔叔节点是黑色 且 自己是父亲节点的右孩子，此时是LR型，应当先左旋再右旋，和LL型的直接右旋相似，所以下文直接右旋操作，并没有讨论LL型
			elif node == node.p.right:
				left_rotate(self, node.p)
				node = node.left
				
			node.p.color = BLACK
			node.p.p.color = RED
			right_rotate(self, node.p.p)
			return
		# 对称的讨论
		elif node.p == node.p.p.right:
			node_uncle = node.p.p.left
			if node_uncle and node_uncle.color == RED:
				node.p.color = BLACK
				node_uncle.color = BLACK
				node.p.p.color = RED
				node = node.p.p
				continue
			elif node == node.p.left:
				right_rotate(self, node)
				node = node.right
			node.p.color = BLACK
			node.p.p.color = RED
			left_rotate(self, node.p.p)
			return
	# 最后记得把根节点的颜色改为黑色 保证红黑树特性
	self.root.color = BLACK

```

参考：[链接](https://blog.csdn.net/weixin_42887391/article/details/82631642)
## 红黑树的删除
具体原理不细讲，这边对于进行一个操作的总结：

首先就是先进行删除（实际上和二叉排序树的删除方法也是一样的），删除的位置根据下面讲述的进行判断，删除后，再进行下面的颜色调换。

删除的过程：

```python
if(删除的元素是子节点):
	直接删除即可
elif(删除的元素包含一个子节点（左或者右 有且只有一个）):
	将子节点替换该元素即可
elif(删除的元素包含两个子节点（左且右）):
	寻找其前驱节点或者后继节点（这边以后继节点为例子）
	后继节点替代该位置
	删除的元素变成这个后继节点的位置，然后再次进行判断满足情况1和情况2 再执行对应的操作。
	
```

删除调整颜色的过程：
以下的例子：黑+红指的是删除的元素原本是黑色，然后删除之后顶替上来的元素是红色的元素。
```python
if(根节点):
	该节点变成黑色
elif(红+黑节点):
	该节点变成黑色
elif(黑+红节点):
	该节点变成黑色
elif(黑+黑节点):
	if(兄弟节点是红色):
		兄弟变成黑色
		父亲变成红色
		左旋
		# 这个时候会发现此时新的位置下N的兄弟节点一定是黑色的，此时进入下面情况的判断。
		重置N兄弟节点
	elif(兄弟节点是黑色 且  兄弟节点的两个孩子都是黑色):
		把兄弟节点变成红色
		将父节点变成新的N节点进入循环讨论满足什么情况。
		# （这里相当于就是把N节点的一层黑转移到父节点上，假如此时父节点是红色，就进入红+黑进行判断 如果父节点是黑色，就进入黑+黑进行判断后续的操作）
	elif(兄弟节点是黑色 且 兄弟节点的左孩子是红色 右孩子是黑色):
		对左侄子和兄弟节点进行交换颜色
		对兄弟节点进行右旋
		# 此时再次进入下一层的情况讨论
		重新定义兄弟的右孩子节点
	elif(兄弟节点是黑色 且 兄弟节点的右孩子是红色的):
		父节点和兄弟节点交换颜色
		右侄子变黑
		以父亲节点为支点左旋
```


```python
# 红黑树
class RedBlackTree(object):
def __init__(self):
	self.root = None

# 先删除元素
def delete(self, node):
	# 找到以该节点为根节点的右子树的最小节点
	node_color = node.color
	# 这边实际上也已经考虑到node节点后面啥都没有，默认用right进行替换
	if not node.left:
		temp_node = node.right
		transplant(self, node, node.right)
	elif not node.right:
		temp_node = node.left
		transplant(self, node, node.left)
	else:
	# 最麻烦的一种情况 既有左子 又有右子 找到右子中最小的做替换类似于二分查找树的删除
		node_min = tree_minimum(node.right)
		node_color = node_min.color
		temp_node = node_min.right
		if node_min.p != node:
			transplant(self, node_min, node_min.right)
			node_min.right = node.right
			node_min.right.p = node_min
		transplant(self, node, node_min)
		node_min.left = node.left
		node_min.left.p = node_min
		node_min.color = node.color
	# 当删除的节点的颜色为黑色时 需要调整红黑树
	if node_color == BLACK:
		self.delete_fixup(temp_node)

# 删除之后调整元素的颜色
def delete_fixup(self, node):
	# 实现过程还需要理解 比如为什么要删除 为什么是那几种情况
	while node != self.root and node.color == BLACK:
		if node == node.p.left:
			node_brother = node.p.right
			if node_brother.color == RED:
				node_brother.color = BLACK
				node.p.color = RED
				left_rotate(self, node.p)
				node_brother = node.p.right
			if (not node_brother.left or node_brother.left.color ==BLACK) and (not node_brother.right or node_brother.right.color == BLACK):
				node_brother.color = RED
				node = node.p
				continue
			else:
				if not node_brother.right or node_brother.right.color ==BLACK:
					node_brother.color = RED
					node_brother.left.color = BLACK
					right_rotate(self, node_brother)
					node_brother = node.p.right
				node_brother.color = node.p.color
				node.p.color = BLACK
				node_brother.right.color = BLACK
				left_rotate(self, node.p)
			node = self.root
			break
		else:
			node_brother = node.p.left
			if node_brother.color == RED:
				node_brother.color = BLACK
				node.p.color = RED
				left_rotate(self, node.p)
				node_brother = node.p.right
			if (not node_brother.left or node_brother.left.color ==BLACK) and (not node_brother.right or node_brother.right.color == BLACK):
				node_brother.color = RED
				node = node.p
				continue
			else:
				if not node_brother.left or node_brother.left.color == BLACK:
					node_brother.color = RED
					node_brother.right.color = BLACK
					left_rotate(self, node_brother)
					node_brother = node.p.left
				node_brother.color = node.p.color
				node.p.color = BLACK
				node_brother.left.color = BLACK
				right_rotate(self, node.p)
			node = self.root
			break
	node.color = BLACK


```



测试用的代码：

```python
def main():
	number_list = (7, 4, 1, 8, 5, 2, 9, 6, 3)
	tree = RedBlackTree()
	for number in number_list:
		node = RedBlackTreeNode(number)
		tree.insert(node)
		del node
		
	preorder_tree_walk(tree.root)
	rbtree_print(tree.root, tree.root.value, 0)
	# 删除
	tree.delete(tree.root)
	preorder_tree_walk(tree.root)

if __name__ == '__main__':
	main()
```


参考：[链接](https://blog.csdn.net/weixin_42887391/article/details/82690811)
