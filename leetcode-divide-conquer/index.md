# Leetcode：树的Divide & Conquer


<!-- {{< figure src="/leetcode-divide-conquer/mindmaster.png" title="Title" class="center" width="220" height="196" >}} -->

{{< figure src="/leetcode-divide-conquer/mindmaster.png" class="center" width="600" >}}



## 100. Same Tree (easy)

简述：判断两棵二叉树是否相同

思路：分治法

联系：模板题

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

# class Solution(object):
# 	def isSameTree(self, p, q):
# 		"""
# 		:type p: TreeNode
# 		:type q: TreeNode
# 		:rtype: bool
# 		"""
# 		if p is None and q is None:
# 			return True
# 		if p is None or q is None:
# 			return False
		return p.val == q.val and \
				self.isSameTree(p.left, q.left) and \
				self.isSameTree(p.right, q.right)
```

## 572. Subtree of Another Tree (easy)

简述：判断t是否为s的子树（相同也算子树）

思路：构造`isSame()`辅助

联系：出自100

```python
# class Solution(object):
# 	def isSubtree(self, s, t):
# 		"""
# 		:type s: TreeNode
# 		:type t: TreeNode
# 		:rtype: bool
# 		"""
# 		if s is None:
# 			return False
		return self.isSame(s, t) or self.isSubtree(s.left, t) or self.isSubtree(s.right, t)
		
# 	def isSame(self, s, t):
# 		if s is None and t is None:
# 			return True
# 		if s is None or t is None:
# 			return False
# 		return s.val == t.val and \
# 				self.isSame(s.left, t.left) and \
# 				self.isSame(s.right, t.right)
```

## 101. Symmetric Tree (easy)

简述：判断二叉树是否对称

思路：改造为判断两棵树是否对称

联系：出自100

```python
# class Solution(object):
# 	def isSymmetric(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: bool
# 		"""
# 		if root is None:
# 			return True
# 		return self._isSymmetric(root.left, root.right)
	
	# 要点1-1: 下划线命名helper函数，添加参数
	def _isSymmetric(self, r1, r2):
# 		if r1 is None and r2 is None:
# 			return True
# 		if r1 is None or r2 is None:
# 			return False
# 		return r1.val == r2.val and \
# 				self._isSymmetric(r1.left, r2.right) and \
# 				self._isSymmetric(r1.right, r2.left)
```

## 270. Closest Binary Search Tree Value (easy)

简述：在BST里找最接近目标的值

思路：分治

联系：出自100

```python
# class Solution(object):
# 	def closestValue(self, root, target):
# 		"""
# 		:type root: TreeNode
# 		:type target: float
# 		:rtype: int
# 		"""
# 		if root.left is None and root.right is None:
# 			return root.val
# 		if target == root.val:
# 			return root.val
# 		if target > root.val:
# 			if root.right is None:
# 				return root.val
# 			tmp_res = self.closestValue(root.right, target)
			if abs(root.val - target) <= abs(tmp_res - target):
# 				return root.val
# 			else:
# 				return tmp_res
# 		else:
# 			if root.left is None:
# 				return root.val
# 			tmp_res = self.closestValue(root.left, target)
			if abs(root.val - target) <= abs(tmp_res - target):
# 				return root.val
# 			else:
# 				return tmp_res
```

## 104. Maximum Depth of Binary Tree (easy)

简述：求二叉树最大深度, 通常定义如下 （此题按节点数量算深度）

I learned that depth and height are properties of a node:

* The depth of a node is the number of edges from the node to the tree's root node.
A root node will have a depth of 0.
* The height of a node is the number of edges on the longest path from the node to a leaf.
A leaf node will have a height of 0.

Properties of a tree:

* The height of a tree would be the height of its root node,
or equivalently, the depth of its deepest node.
* The diameter (or width) of a tree is the number of nodes on the longest path between any two leaf nodes. The tree below has a diameter of 6 nodes.

![](https://i.stack.imgur.com/8yPi9.png)

depth往根（上）数，height往最远的叶（下）数，height等于maxDepth

思路：分治法，也可以遍历法

联系：出自100

```python
# class Solution(object):
# 	def maxDepth(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		if root is None:
# 			return 0
# 		return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1
```

## 111. Minimum Depth of Binary Tree (easy)

简述：求二叉树最小深度

思路：分治法

联系：出自104

```python
# class Solution(object):
# 	def minDepth(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		if root is None:
# 			return 0
		if root.left is None and root.right is None:
			return 1
		if root.left is None:
			return self.minDepth(root.right) + 1
		if root.right is None:
			return self.minDepth(root.left) + 1
# 		return min(self.minDepth(root.left), self.minDepth(root.right)) + 1
```

## 226. Invert Binary Tree (easy)

简述：左右反转二叉树

思路：分治

联系：出自100

```python
# class Solution(object):
# 	def invertTree(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if root.left is None and root.right is None:
# 			return root
		root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
# 		return root
```

## 669. Trim a Binary Search Tree (easy)

简述：裁剪BST，使得裁剪后元素位于区间\[L, R\](R>=L)内

思路：根节点三种情况：<L, >R, LR之间

联系：出自100

```python
# class Solution(object):
# 	def trimBST(self, root, L, R):
# 		"""
# 		:type root: TreeNode
# 		:type L: int
# 		:type R: int
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if root.val < L:
# 			return self.trimBST(root.right, L, R)
# 		if root.val > R:
# 			return self.trimBST(root.left, L, R)
		root.left, root.right = self.trimBST(root.left, L, R), self.trimBST(root.right, L, R)
# 		return root
```

## 110. Balanced Binary Tree (easy)

简述：判断二叉树是否平衡，即每个节点左右子树高度差不超过1

思路：分治+全局变量

联系：出自104

```python
# class Solution(object):
# 	def isBalanced(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: bool
# 		"""
		# 要点2-1: 添加全局变量
		self._isBalanced = True
# 		height = self.findHeight(root)
# 		return self._isBalanced
		
# 	def findHeight(self, root):
		# 要点2-2: 添加短路判断
		if root is None or not self._isBalanced:
			return 0
		
# 		left_height = self.findHeight(root.left)
# 		right_height = self.findHeight(root.right)
		
# 		if abs(left_height - right_height) > 1:
# 			self._isBalanced = False
			
# 		return max(left_height, right_height) + 1
```

## 543. Diameter of Binary Tree (easy)

简述：求二叉树的直径，即节点间的最长路径（可不经过根节点）

思路：分治+全局变量

联系：出自110

```python
# class Solution(object):
# 	def diameterOfBinaryTree(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
		# 要点1-1: 添加全局变量
		self.res = 0
# 		self.findDepth(root)
# 		return self.res
		
# 	def findDepth(self, root):
# 		if root is None:
# 			return 0
# 		left_depth = self.findDepth(root.left)
# 		right_depth = self.findDepth(root.right)
		
		self.res = max(self.res, left_depth + right_depth)
# 		return max(left_depth, right_depth) + 1
```

## 124. Binary Tree Maximum Path Sum (hard)

简述：求二叉树中路径最大和（任意节点到任意节点，不必经过根节点）

思路：分治+全局变量

联系：出自110

```python
# class Solution(object):
# 	def maxPathSum(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
		# 要点1-1: python最小整数
		self.res = -sys.maxsize - 1
# 		self.maxSumFromRoot(root)
# 		return self.res
		
# 	def maxSumFromRoot(self, root):
# 		if root is None:
# 			return 0
# 		if root.left is None and root.right is None:
# 			self.res = max(self.res, root.val)
# 			return root.val
# 		left_max = self.maxSumFromRoot(root.left)
# 		right_max = self.maxSumFromRoot(root.right)
		self.res = max(self.res, root.val + \
					   max(0, left_max, right_max, left_max + right_max))
# 		return max(root.val, root.val + left_max, root.val + right_max)
```

## 257. Binary Tree Paths (easy)

简述：打印二叉树所有根到叶路径

思路：分治

联系：出自100

```python
# class Solution(object):
# 	def binaryTreePaths(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: List[str]
# 		"""
# 		if root is None:
# 			return []
# 		if root.left is None and root.right is None:
# 			return [str(root.val)]
# 		left_paths = self.binaryTreePaths(root.left)
# 		right_paths = self.binaryTreePaths(root.right)
		
# 		res = []
# 		res += [str(root.val) + '->' + p for p in left_paths]
# 		res += [str(root.val) + '->' + p for p in right_paths]
# 		return res
```

## 654. Maximum Binary Tree (medium)

简述：给数组，按规则构造树，规则：数组最大值为根，最大值左边为左子树，最大值右边为右子树，左右子树继续符合规则

思路：递归定义，分治

联系：出自100

```python
# class Solution(object):
# 	def constructMaximumBinaryTree(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: TreeNode
# 		"""
# 		if not nums:
# 			return None
# 		root = TreeNode(max(nums))
# 		root.left = self.constructMaximumBinaryTree(nums[0:nums.index(max(nums))])
# 		root.right = self.constructMaximumBinaryTree(nums[nums.index(max(nums))+1:])
# 		return root
```

## 108. Convert Sorted Array to Binary Search Tree (easy)

简述：给已排序的数组，构造相应的平衡BST

思路：中点作为pivot

联系：出自654

```python
# class Solution(object):
# 	def sortedArrayToBST(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: TreeNode
# 		"""
# 		if not nums:
# 			return None
# 		mid = len(nums) // 2
# 		root = TreeNode(nums[mid])
# 		root.left = self.sortedArrayToBST(nums[0: mid])
# 		root.right = self.sortedArrayToBST(nums[mid + 1:])
# 		return root
```

## 606. Construct String from Binary Tree (easy)

简述：按preorder规则通过添加括号把二叉树转为字符串，以保证树与字符串的一一对应。

思路：如果左子树为空，可添加一对空括号

联系：出自654

```python
# class Solution(object):
# 	def tree2str(self, t):
# 		"""
# 		:type t: TreeNode
# 		:rtype: str
# 		"""
# 		if t is None:
# 			return ""
# 		if t.left is None and t.right is None:
# 			return str(t.val)
		if t.left is None:
			return str(t.val) + '()(' + self.tree2str(t.right) + ')'
		if t.right is None:
			return str(t.val) + '(' + self.tree2str(t.left) + ')'
# 		return str(t.val) + '(' + self.tree2str(t.left) + ')(' + self.tree2str(t.right) + ')'
```

## 1120. Maximum Average Subtree (medium)

简述：求子树中最大平均值

思路：维护平均值，辅助函数返回树的总和与总节点个数

联系：出自110

```python
# class Solution(object):
# 	def maximumAverageSubtree(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: float
# 		"""
		# 要点1-1: 初始为最小浮点数
		self.res = float('-inf')
# 		res_cnt, res_sum = self.findSum(root)
# 		return self.res
		
		
# 	def findSum(self, root):
# 		if root is None:
# 			return 0, 0
# 		if root.left is None and root.right is None:
# 			self.res = max(self.res, root.val)
# 			return 1, root.val
# 		left_cnt, left_sum = self.findSum(root.left)
# 		right_cnt, right_sum = self.findSum(root.right)
		
# 		res_cnt = left_cnt + right_cnt + 1
# 		res_sum = left_sum + right_sum + root.val
# 		self.res = max(self.res, res_sum * 1.0 / res_cnt)
# 		return res_cnt, res_sum
```


## 563. Binary Tree Tilt (easy)

简述：按定义求树的tilt，定义：节点tilt-左子树节点和与右子树节点和差值的绝对值，树的tilt-所有节点tilt的和

思路：辅助函数`findSum()`, 维护全局变量

联系：出自110

```python
# class Solution(object):
# 	def findTilt(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		self.res = 0
# 		total_sum = self.findSum(root)
# 		return self.res
		
# 	def findSum(self, root):
# 		if root is None:
# 			return 0
# 		if root.left is None and root.right is None:
# 			return root.val
# 		left_sum, right_sum = self.findSum(root.left), self.findSum(root.right)

		self.res += abs(left_sum - right_sum)
		return left_sum + right_sum + root.val
```

## 366. Find Leaves of Binary Tree (medium)

简述：二叉树剥洋葱，返回最外层（叶节点），然后次外层（倒数第二层节点），以此类推

思路：标注节点高度，构造`节点->高度`映射，再返回反向索引

联系：出自110

```python
# class Solution(object):
# 	def findLeaves(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: List[List[int]]
# 		"""
		# 要点2-1: 全局变量，映射
		height_memo = dict()
# 		self.label_height(root, height_memo)

# 		# 要点2-2: 反向索引inverted index
		return self.convert_memo(height_memo)
		
# 	def label_height(self, root, height_memo):
# 		if root is None:
# 			return 0
# 		if root.left is None and root.right is None:
# 			height_memo[root] = 1
# 			return 1
# 		left_height = self.label_height(root.left, height_memo)
# 		right_height = self.label_height(root.right, height_memo)
		
# 		res_height = max(left_height, right_height) + 1
# 		height_memo[root] = res_height
# 		return res_height
	
# 	def convert_memo(self, height_memo):
# 		res = dict()
# 		for k, v in height_memo.items():
# 			if v not in res:
# 				res[v] = list()
# 			res[v].append(k.val)
# 		ans = []
# 		for i in range(len(res.keys())):
# 			ans.append(res[i + 1])
# 		return ans
```

## 617. Merge Two Binary Trees (easy)

简述：合并两棵二叉树，合并后节点为两棵对应节点之和

思路：分治

联系：出自669

```python
# class Solution(object):
# 	def mergeTrees(self, t1, t2):
# 		"""
# 		:type t1: TreeNode
# 		:type t2: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if t1 is None:
# 			return t2
# 		if t2 is None:
# 			return t1
# 		root = TreeNode(t1.val + t2.val)
# 		root.left = self.mergeTrees(t1.left, t2.left)
# 		root.right = self.mergeTrees(t1.right, t2.right)
# 		return root
```

## 1490. Clone N-ary Tree (medium)

简述：深拷贝N元树

思路：递归定义，分治

联系：出自100

```python
# class Solution(object):
# 	def cloneTree(self, root):
# 		"""
# 		:type root: Node
# 		:rtype: Node
# 		"""
# 		if root is None:
# 			return None
# 		root_copy = Node(val=root.val)
# 		for el in root.children:
# 			root_copy.children.append(self.cloneTree(el))
# 		return root_copy
```

## 814. Binary Tree Pruning (medium)

简述：二叉树树中节点只能为0或1，裁剪二叉树，使得最后没有全为0的子树

思路：分治

联系：出自100

```python
# class Solution(object):
# 	def pruneTree(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if root.left is None and root.right is None:
# 			if root.val == 0:
# 				return None
# 			else:
# 				return root
# 		left_pruned = self.pruneTree(root.left)
# 		right_pruned = self.pruneTree(root.right)
		
		if left_pruned is None and right_pruned is None and root.val == 0:
			return None
# 		root.left, root.right = left_pruned, right_pruned
# 		return root
```

## 236. Lowest Common Ancestor of a Binary Tree (medium)

简述：求两节点在二叉树里的LCA（假设两节点都在树中）

思路：分治

联系：出自100

```python
# class Solution(object):
	# 要点1-1: 三种情况：1）两个节点都在树中返回lca， 2）一个在树中返回该节点，3）两个都不在返回None
	def lowestCommonAncestor(self, root, p, q):
# 		"""
# 		:type root: TreeNode
# 		:type p: TreeNode
# 		:type q: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if root == p or root == q:
# 			return root
# 		left_lca = self.lowestCommonAncestor(root.left, p, q)
# 		right_lca = self.lowestCommonAncestor(root.right, p, q)
# 		if left_lca and right_lca:
# 			return root
# 		if left_lca:
# 			return left_lca
# 		if right_lca:
# 			return right_lca
# 		return None
```

## 235. Lowest Common Ancestor of a Binary Search Tree (easy)

简述：求两节点在BST里的LCA（假设两节点都在树中）

思路：分治，根据情况走一边子树

联系：出自236

```python
# class Solution(object):
# 	def lowestCommonAncestor(self, root, p, q):
# 		"""
# 		:type root: TreeNode
# 		:type p: TreeNode
# 		:type q: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if root == p or root == q:
# 			return root
# 		if p.val < root.val < q.val or q.val < root.val < p.val:
# 			return root
# 		if root.val < p.val:
# 			return self.lowestCommonAncestor(root.right, p, q)
# 		else:
# 			return self.lowestCommonAncestor(root.left, p, q)
```

## 156. Binary Tree Upside Down (medium)

简述：上下翻转二叉树，所给二叉树的性质：右节点是空或是有兄弟节点的叶节点

思路：画图按例子翻转

联系：出自226

```python
# class Solution(object):
# 	def upsideDownBinaryTree(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if root.left is None and root.right is None:
# 			return root
# 		res = self.upsideDownBinaryTree(root.left)
		root.left.left, root.left.right = root.right, root
# 		root.left, root.right = None, None
# 		return res
```

## 114. Flatten Binary Tree to Linked List (medium)

简述：in-place转化二叉树成链表结构

思路：分治

联系：出自226

```python
# class Solution(object):
# 	def flatten(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: None Do not return anything, modify root in-place instead.
# 		"""
# 		tail = self.flatten_tail(root)
		
# 	def flatten_tail(self, root):
# 		if root is None:
# 			return None
# 		if root.left is None and root.right is None:
# 			return root
# 		if root.left is None:
# 			return self.flatten_tail(root.right)
# 		if root.right is None:
# 			left_tail = self.flatten_tail(root.left)
# 			root.left, root.right = None, root.left
# 			return left_tail
# 		left_tail = self.flatten_tail(root.left)
# 		right_tail = self.flatten_tail(root.right)
		  
# 		root.left, root.right, left_tail.right = None, root.left, root.right
# 		return right_tail
```

VERSION II (from [girikuncoro's post](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/discuss/37154/8-lines-of-python-solution-(reverse-preorder-traversal)))

```python
# class Solution(object):
# 	def __init__(self):
		self.prev = None
		
# 	def flatten(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: None Do not return anything, modify root in-place instead.
# 		"""
# 		if root is None:
# 			return
# 		self.flatten(root.right)
# 		self.flatten(root.left)
		
# 		# 要点1-1: 先右子树再左子树，这样会把右子树通过prev移花接木到左子树尾部
		root.right = self.prev
# 		root.left = None
# 		self.prev = root
```

## 298. Binary Tree Longest Consecutive Sequence (medium)

简述：在二叉树中找连续值路径（路径这里指从父节点向下）

思路：分治，维护全局变量

联系：出自110

```python
# class Solution(object):
# 	def longestConsecutive(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		self.res = 0
# 		self.longest_from_root(root)
# 		return self.res
		
# 	def longest_from_root(self, root):
# 		if root is None:
# 			return 0
# 		if root.left is None and root.right is None:
# 			self.res = max(self.res, 1)
# 			return 1
		
		# 要点1-1: 无论当前节点与左右子节点是否差1，都要执行搜索
		left_longest = self.longest_from_root(root.left)
		right_longest = self.longest_from_root(root.right)
		
# 		curr = 1
# 		if root.left and root.val + 1 == root.left.val:
# 			curr = max(curr, left_longest + 1)
# 		if root.right and root.val + 1 == root.right.val:
# 			curr = max(curr, right_longest + 1)
			
# 		self.res = max(self.res, curr)
# 		return curr
```

## 285. Inorder Successor in BST (medium)

简述：BST中找所给节点的下一个按序节点，即比所给节点大的最小节点

思路：分治

联系：出自100

```python
# class Solution(object):
# 	def inorderSuccessor(self, root, p):
# 		"""
# 		:type root: TreeNode
# 		:type p: TreeNode
# 		:rtype: TreeNode
# 		"""
# 		if root is None:
# 			return None
# 		if p.val >= root.val:
# 			return self.inorderSuccessor(root.right, p)
# 		left_res = self.inorderSuccessor(root.left, p)
# 		if left_res is None:
# 			return root
# 		return left_res
```

## 671. Second Minimum Node In a Binary Tree (easy)

简述：二叉树满足：每个节点有0或2个子节点，当2个子节点时，`root.val = min(root.left.val, root.right.val)`，求树中第二小的节点值

思路：分治，注意比较时的初始化

联系：出自100

```python
# class Solution(object):
# 	def findSecondMinimumValue(self, root):
# 		"""
# 		:type root: TreeNode
# 		:rtype: int
# 		"""
# 		if root is None:
# 			return -1
# 		if root.left is None and root.right is None:
# 			return -1
		
# 		left_res = self.findSecondMinimumValue(root.left)
# 		right_res = self.findSecondMinimumValue(root.right)
		
		# 要点1-1: 初始化比较值，题目要求无第二最小值，返回-1
		if left_res == -1:
			left_res = sys.maxsize
		if right_res == -1:
			right_res = sys.maxsize
		
# 		if root.val == root.left.val == root.right.val:
			res = min(left_res, right_res)
			return res if res != sys.maxsize else -1
# 		else:
# 			if root.val == root.left.val:
# 				return min(left_res, root.right.val)
# 			else:
# 				return min(right_res, root.left.val)
```

























