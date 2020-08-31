# Leetcode：树的Traversal


{{< figure src="/leetcode-tree-traversal/mindmaster.png" class="center" width="800" >}}

# In-Order

## 230. Kth Smallest Element in a BST (medium)

简述：在BST中，求第k小的值

思路：inorder，记录遍历idx

联系：模板题

```python
# class Solution(object):
# 	def kthSmallest(self, root, k):
# 		"""
# 		:type root: TreeNode
# 		:type k: int
# 		:rtype: int
# 		"""
# 		self.idx, self.res = 0, None
# 		self.k = k
# 		self.inorder(root)
# 		return self.res
		
# 	def inorder(self, root):
# 		if root is None or self.res is not None:
# 			return
# 		if self.res is not None:
# 			return
# 		self.inorder(root.left)
# 		self.idx += 1
# 		if self.idx == self.k:
# 			self.res = root.val
# 		self.inorder(root.right)
```

## 938. Range Sum of BST (easy)

简述：求BST中值介于[L,R]的节点值的和

思路：inorder + 短路判断

联系：出自230

```python
# class Solution(object):
# 	def rangeSumBST(self, root, L, R):
# 		"""
# 		:type root: TreeNode
# 		:type L: int
# 		:type R: int
# 		:rtype: int
# 		"""
# 		self.res = 0
# 		self.L, self.R = L, R
# 		self.inorder(root)
# 		return self.res
	
# 	def inorder(self, root):
# 		if root is None:
# 			return
# 		self.inorder(root.left)
# 		if self.L <= root.val <= self.R:
# 			self.res += root.val
		if root.val > self.R:
			return
# 		self.inorder(root.right)
```

## 538. Convert BST to Greater Tree (easy)

简述：改变BST各节点值，使得节点值为后缀和

思路：inorder从后到前遍历

联系：出自230

```python
# class Solution(object):
# 	def convertBST(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		self.running_total = 0
# 		self.inorder(root)
# 		return root
		
# 	def inorder(self, root):
# 		if root is None:
# 			return None
# 		self.inorder(root.right)
		root.val += self.running_total
		self.running_total = root.val
# 		self.inorder(root.left)
```

## 501. Find Mode in Binary Search Tree (easy)

简述：求BST中出现最多的值

思路：inorder建立字典

联系：出自230

```python
# class Solution(object):
# 	def findMode(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: List[int]
# 		"""
# 		if root is None:
# 			return []
# 		self.memo = dict()
# 		self.inorder(root)
		# 要点2-1: python语法，总结value
		mode = max(self.memo.values())
		res = [key for key in self.memo if self.memo[key] == mode]
# 		return res
		
# 	def inorder(self, root):
# 		if root is None:
# 			return
# 		self.inorder(root.left)
		# 要点2-2: 异常if用来，初始化key不在字典中的情况
		if root.val not in self.memo:
			self.memo[root.val] = 0
# 		self.memo[root.val] += 1
# 		self.inorder(root.right)
```

## 530. Minimum Absolute Difference in BST (easy)

简述：在BST节点对中，求最小的相差绝对值

思路：BST的in-order将返回排序数组

联系：出自230

```python
# class Solution(object):
# 	def getMinimumDifference(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		self.res = sys.maxsize
		self.prev = None
# 		self.inorder(root)
# 		return self.res
	
# 	def inorder(self, root):
# 		if root is None:
# 			return
# 		self.inorder(root.left)
# 		if self.prev is not None:
# 			self.res = min(self.res, abs(root.val - self.prev))
# 		self.prev = root.val
# 		self.inorder(root.right)
```
## 783. Minimum Distance Between BST Nodes (easy)

简述：在BST节点对中，求最小的距离（相差）

思路：inorder，记录历史

联系：出自530

```python
# class Solution(object):
# 	def minDiffInBST(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		self.prev, self.res = None, sys.maxsize
# 		self.inorder(root)
# 		return self.res
	
# 	def inorder(self, root):
# 		if root is None:
# 			return None
# 		self.inorder(root.left)
		
# 		if self.prev is not None:
# 			self.res = min(self.res, root.val - self.prev)
# 		self.prev = root.val
		
# 		self.inorder(root.right)
```

## 897. Increasing Order Search Tree (easy)

简述：把BST变为单向链表

思路：记录遍历前的node

联系：出自530

```python
# class Solution(object):
# 	def increasingBST(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		self.dummy = TreeNode(0)
# 		self.p = self.dummy
# 		self.inorder(root)
# 		return self.dummy.right
	
# 	def inorder(self, root):
# 		if root is None:
# 			return
# 		self.inorder(root.left)
		
		root.left, self.p.right, self.p.left = None, root, None
		self.p = self.p.right
		
# 		self.inorder(root.right)
```

# Pre-Order

## 144. Binary Tree Preorder Traversal (medium)

简述：二叉树pre-order遍历

思路：recursive简单，主要尝试iterative

联系：

```python
# class Solution(object):
# 	def preorderTraversal(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: List[int]
# 		"""
# 		if root is None:
# 			return []
		# 要点1-1: 用stack存待搜索树
		stack = [root]
# 		res = []
# 		while stack:
# 			node = stack.pop()
# 			res.append(node.val)
# 			if node.right is not None:
# 				stack.append(node.right)
# 			if node.left is not None:
# 				stack.append(node.left)
# 		return res
```

## 965. Univalued Binary Tree (easy)

简述：判断树中节点值是否单一

思路：遍历时，维护全局变量

联系：

```python
# class Solution(object):
# 	def isUnivalTree(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: bool
# 		"""
# 		if root.left is None and root.right is None:
# 			return True
# 		self.rootval, self.res = root.val, True
# 		self.preOrder(root)
# 		return self.res
		
# 	def preOrder(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: bool
# 		"""
# 		if root is None:
# 			return
# 		if root.val != self.rootval:
# 			self.res = False
# 		self.preOrder(root.left)
# 		self.preOrder(root.right)
```

# Post-Order

# backtrack

## 404. Sum of Left Leaves (easy)

简述：求二叉树中，所有左叶节点和

思路：左转向下时标注“左”，右转向下时标注“右”

联系：

```python
# class Solution(object):
# 	def sumOfLeftLeaves(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		self.res = 0
		self.inorder(root, False)
# 		return self.res
	
# 	def inorder(self, root, isLeft):
# 		if root is None:
# 			return
# 		self.inorder(root.left, True)
# 		if root.left is None and root.right is None and isLeft:
# 				self.res += root.val
# 		self.inorder(root.right, False)
```

# Non-recursive

## 510. Inorder Successor in BST II (medium)

简述：给二叉树中某个节点，树中找in-order顺序中的下一个节点（此题节点额外含有：子->父）

思路：while

联系：

```python
# class Solution(object):
# 	def inorderSuccessor(self, node):
# 		"""
# 		:type node: Node
# 		:rtype: Node
# 		"""
# 		if node is None:
# 			return None
# 		if node.right:
# 			curr = node.right
# 			while curr.left:
# 				curr = curr.left
# 			return curr
# 		else:
# 			curr = node
# 			while curr.parent and curr == curr.parent.right:
# 				curr = curr.parent
# 			return curr.parent
```


## 222. Count Complete Tree Nodes (medium)

简述：complete二叉树 （complete指从上到下，从左到右尽量排满），求树中节点个数

思路：最优复杂度O(logN\*logN)

联系：树与二分法很漂亮的结合题

```python
# class Solution(object):
# 	def countNodes(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		if root is None:
# 			return 0
# 		height = self.findHeight(root)
# 		res_full = 2**height - 1
		
		# 要点3-1: 二分法模板
# 		left, right = 1, 2**height
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if self.pathHasNode(root, height, mid - 1):
# 				left = mid
# 			else:
# 				right = mid
		
# 		if self.pathHasNode(root, height, right - 1):
# 			return res_full + right
# 		else:
# 			return res_full + left
	
# 	def findHeight(self, root):
# 		res, p = 0, root
# 		while p.left:
# 			res += 1
# 			p = p.left
# 		return res
	
	# 要点3-2: path encoding
	def pathHasNode(self, root, height, encoding_int):
# 		p = root
		# 要点3-3: 构造mask，将encoded path从高位到地位二进制扫描出来
# 		mask = 2**(height-1)
# 		for _ in range(height):
			if encoding_int & mask == 0:
# 				if p.left is None:
# 					return False
# 				p = p.left
# 			else:
# 				if p.right is None:
# 					return False
# 				p = p.right
			mask >>= 1
# 		return True
```

## 701. Insert into a Binary Search Tree (medium)

简述：将所给值插入BST（假设原BST不含所给值）

思路：用BST性质

联系：

```python
# class Solution(object):
# 	def insertIntoBST(self, root, val):
# 		"""
# 		:type root: TreeNode
# 		:type val: int
# 		:rtype: TreeNode
# 		"""
		# 要点1-1：边界情况
		if root is None:
			return TreeNode(val)
# 		p = root
# 		while True:
# 			if val > p.val:
# 				if p.right is None:
# 					p.right = TreeNode(val)
# 					return root
# 				else:
# 					p = p.right
# 			else:
# 				if p.left is None:
# 					p.left = TreeNode(val)
# 					return root
# 				else:
# 					p = p.left
```

## 94. Binary Tree Inorder Traversal (medium)

简述：不用递归，in-order遍历二叉树

思路：左子树为优先级

联系：出自510

```python
# class Solution(object):
# 	def inorderTraversal(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: List[int]
# 		"""
		if root is None:
			return []
		dummy = TreeNode(0)
		dummy.right = root
		stack = [dummy]
			
		inorder = []
		while stack:
			node = stack.pop()
			if node.right:
				# 想处理此节点，先把此节点的右子树加入优先级栈
				node = node.right
				while node:
					stack.append(node)
					node = node.left
			if stack:
				inorder.append(stack[-1].val)
				
		return inorder
```
























































