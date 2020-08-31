# Leetcode：二分查找


# 模板

```python
# def binarySearch(nums, target):
# 	if len(nums) == 0:
# 		return -1
# 	start, end = 0, len(nums) - 1
	while start + 1 < end: # 要点3-1
		mid = (start + end) // 2 # 要点3-2: python动态数据类型
# 		if nums[start] < target:
			start = mid # 要点3-3: mid不+-1
# 		else:
# 			end = mid

# 	if nums[start] == target:
# 		return start
# 	if nums[end] == target:
# 		return end
# 	return -1
```

![](/leetcode-binary-search/mindmaster.png)

<!-- []()  | 
------|------
![find-position](/leetcode-binary-search/find-position.png) | ![non-mono](/leetcode-binary-search/non-mono.png)
![find-status](/leetcode-binary-search/find-status.png) | ![binary-answer](/leetcode-binary-search/binary-answer.png) -->

# 找位置

## 34. Find First and Last Position of Element in Sorted Array (medium)
简述：已排序数组，求目标值第一次和最后一次出现的位置

思路：拆解为两个子函数，`findFirst()`和`findLast()`

联系：基础模板题

```python
# class Solution(object):
# 	def searchRange(self, nums, target):
# 		"""
# 		:type nums: List[int]
# 		:type target: int
# 		:rtype: List[int]
# 		"""
# 		first, last = self.findFirst(nums, target), self.findLast(nums, target)
# 		return [first, last]
		
		
# 	def findFirst(self, nums, target):
# 		if len(nums) == 0:
# 			return -1
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
			if nums[mid] < target: # 要点2-1
# 				left = mid
# 			else:
# 				right = mid
# 		if nums[left] == target:
# 			return left
# 		if nums[right] == target:
# 			return right
# 		return -1
	
# 	def findLast(self, nums, target):
# 		if len(nums) == 0:
# 			return -1
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
			if nums[mid] > target: # 要点2-2
# 				right = mid
# 			else:
# 				left = mid
# 		if nums[right] == target:
# 			return right
# 		if nums[left] == target:
# 			return left
# 		return -1
```

## 35. Search Insert Position (easy)
简述：已排序数组，求插入位置保证插入后依然排序

思路：类似`findFirst()`

联系：出自34

```python
# class Solution(object):
# 	def searchInsert(self, nums, target):
# 		"""
# 		:type nums: List[int]
# 		:type target: int
# 		:rtype: int
# 		"""
# 		# 要点1-1: 注意输入边界情况
		if target < nums[0]:
			return 0
		if target > nums[-1]:
			return len(nums)
		
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if nums[mid] < target:
# 				left = mid
# 			else:
# 				right = mid
# 		if nums[left] >= target:
# 			return left
# 		return right
```

## 744. Find Smallest Letter Greater Than Target (easy)

简述：已排序的数组，求大于目标的最前位置

思路：类似`findFirst()`

联系：出自34

```python
# class Solution(object):
# 	def nextGreatestLetter(self, letters, target):
# 		"""
# 		:type letters: List[str]
# 		:type target: str
# 		:rtype: str
# 		"""
# 		left, right = 0, len(letters) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if letters[mid] <= target:
# 				left = mid
# 			else:
# 				right = mid
# 		if letters[left] > target:
# 			return letters[left]
# 		if letters[right] > target:
# 			return letters[right]
# 		return letters[0]
```

## 702. Search in a Sorted Array of Unknown Size (medium)
简述：已排序数组，但不知size，求目标值位置

思路：扩右边直到包含目标值，再二分

联系：出自34

```python
# class Solution(object):
# 	def search(self, reader, target):
# 		"""
# 		:type reader: ArrayReader
# 		:type target: int
# 		:rtype: int
# 		"""
# 		# 要点1-1: 倍增右边界
		right = 1
		while reader.get(right) < target:
			right *= 2
		left = right // 2
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if reader.get(mid) == target:
# 				return mid
# 			if reader.get(mid) < target:
# 				left = mid
# 			else:
# 				right = mid
# 		if reader.get(left) == target:
# 			return left
# 		if reader.get(right) == target:
# 			return right
# 		return -1
```

## 74. Search a 2D Matrix (medium)

简述：已排序的2D数组(每行排序，一行最后元素小于下行最前元素)，求目标值是否存在

思路：只是1D数组layout变化，模板不变

联系：出自34

```python
# class Solution(object):
# 	def searchMatrix(self, matrix, target):
# 		"""
# 		:type matrix: List[List[int]]
# 		:type target: int
# 		:rtype: bool
# 		"""
# 		if not matrix or not matrix[0]:
# 			return False
# 		M, N = len(matrix), len(matrix[0])
# 		left, right = 0, M * N - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			row, col = mid // N, mid % N
# 			if matrix[row][col] == target:
# 				return True
# 			if matrix[row][col] < target:
# 				left = mid
# 			else:
# 				right = mid
# 		return matrix[left//N][left%N] == target or matrix[right//N][right%N] == target
```

## 240. Search a 2D Matrix II (medium)

简述：已排序的2D数组(每行排序，每列排序)，求目标值是否存在

思路：斜对角线一个个坐标走，不是二分题

联系：出自74，不是二分

```python
# class Solution(object):
# 	def searchMatrix(self, matrix, target):
# 		"""
# 		:type matrix: List[List[int]]
# 		:type target: int
# 		:rtype: bool
# 		"""
# 		if not matrix or not matrix[0]:
# 			return False
# 		M, N = len(matrix), len(matrix[0])
# 		x, y = M - 1, 0
# 		while True:
# 			if not (0 <= x < M and 0 <= y < N):
# 				return False
# 			if matrix[x][y] == target:
# 				return True
# 			# 要点1-1: 不是二分，走坐标
			if matrix[x][y] > target:
				x -= 1
			else:
				y += 1
# 		return False
```

## 658. Find K Closest Elements (medium)

简述：已排序数组，求k个最邻近目标的元素们

思路：先找到最邻近目标的元素，再往两边扩展

联系：出自34

```python
# class Solution(object):
# 	def findClosestElements(self, arr, k, x):
# 		"""
# 		:type arr: List[int]
# 		:type k: int
# 		:type x: int
# 		:rtype: List[int]
# 		"""
# 		left, right = 0, len(arr) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if arr[mid] < x:
# 				left = mid
# 			else:
# 				right = mid
# 		# 要点1-1: 最后的范围左右开区间(left, right)
		return self.expand(arr, left, right, k, x)
	
# 	def expand(self, arr, left, right, k, x):
# 		cnt = 0
# 		while cnt < k:
# 			if right >= len(arr) or abs(arr[left] - x) <= abs(arr[right] - x):
# 				left -= 1
# 			else:
# 				right += 1
# 			cnt += 1
		return arr[left + 1 : left + 1 + k]
```

# 非单调

## 153. Find Minimum in Rotated Sorted Array (medium)

简述：rotate过的排序数组(无重复元素)，求最小值

思路：pivot出现在前半段，经过顺序为 中<右<左，pivot出现在后半段，经过顺序为 右<左<中

联系：左右关系无法判断, 应判断中右关系

```python
# class Solution(object):
# 	def findMin(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: int
# 		"""
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
			if nums[mid] > nums[right]:
# 				left = mid
# 			else:
# 				right = mid
# 		return min(nums[left], nums[right])
```

## 33. Search in Rotated Sorted Array (medium)

简述：rotate过的排序数组(无重复元素)，求目标值位置

思路：先找pivot，再传统二分

联系：出自153

```python
# class Solution(object):
# 	def search(self, nums, target):
# 		"""
# 		:type nums: List[int]
# 		:type target: int
# 		:rtype: int
# 		"""
# 		if len(nums) == 0:
# 			return -1
# 		pivot = self.findPivot(nums)
# 		L = len(nums)
# 		left, right = 0, L - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			# 要点1-1: 访问排序数组时转化坐标
			if nums[(mid + pivot) % L] > target:
# 				right = mid
# 			else:
# 				left = mid
# 		if nums[(left + pivot) % L] == target:
# 			return (left + pivot) % L
# 		if nums[(right + pivot) % L] == target:
# 			return (right + pivot) % L
# 		return -1
		
	def findPivot(self, nums):
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if nums[mid] > nums[right]:
# 				left = mid
# 			else:
# 				right = mid
# 		if nums[left] < nums[right]:
# 			return left
# 		else:
# 			return right
```

## 154. Find Minimum in Rotated Sorted Array II (hard)

简述：rotate过的排序数组(有重复元素)，求最小元素

思路：11111011 (最坏情况linear查找)

联系：出自33

```python
# class Solution(object):
# 	def findMin(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: int
# 		"""
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if nums[right] > nums[mid]:
# 				right = mid
# 			elif nums[right] < nums[mid]:
# 				left = mid
			else: # 要点1-1: 如何把线性查找与二分查找合并
				right = right - 1
# 		return min(nums[left], nums[right])
```

## 852. Peak Index in a Mountain Array (easy)

简述：山形数组，求山峰位置

思路：思考山峰若在前半段发生什么(A[mid-1] > A[mid] > A[mid+1])，若在后半段发生什么(A[mid-1] < A[mid] < A[mid+1])

联系：思考方式出自153，都是在想目标出现前后半段的性质差别，二分保证充要

```python
# class Solution(object):
# 	def peakIndexInMountainArray(self, A):
# 		"""
# 		:type A: List[int]
# 		:rtype: int
# 		"""
# 		left, right = 0, len(A) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if A[mid] > A[mid+1] and A[mid] > A[mid-1]:
# 				return mid
# 			if A[mid] > A[mid+1] and A[mid] < A[mid-1]:
# 				right = mid
# 			else:
# 				left = mid
# 		if A[left] > A[right]:
# 			return left
# 		else:
# 			return right
```

## 162. Find Peak Element (medium)

简述：重峦叠嶂数组(相邻不等)，求任意一个山峰位置

思路：类似852，但并不充要

```python
# class Solution(object):
# 	def findPeakElement(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: int
# 		"""
# 		left, right = 0, len(nums) - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if nums[mid] > nums[mid - 1] and nums[mid] > nums[mid + 1]:
# 				return mid
# 			if nums[mid] > nums[mid - 1] and nums[mid] < nums[mid + 1]:
# 				left = mid
# 			else:
# 				right = mid
# 		if nums[left] > nums[right]:
# 			return left
# 		else:
# 			return right
```

# 找状态

## 278. First Bad Version (easy)
简述：OOOOXXX, 求第一个失败版本idx

思路：目标出现前后半段导致mid状态差别

联系：

```python
# # The isBadVersion API is already defined for you.
# # @param version, an integer
# # @return a bool
# # def isBadVersion(version):

# class Solution(object):
# 	def firstBadVersion(self, n):
# 		"""
# 		:type n: int
# 		:rtype: int
# 		"""
# 		left, right = 1, n
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if isBadVersion(mid):
# 				right = mid
# 			else:
# 				left = mid
# 		if isBadVersion(left):
# 			return left
# 		return right
```

## 374. Guess Number Higher or Lower (easy)

简述：1-n猜数字，api回答高低，求目标数字

思路：从目标开始后面都会猜高

联系：出自278

```python
# # The guess API is already defined for you.
# # @param num, your guess
# # @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
# # def guess(num):

# class Solution(object):
# 	def guessNumber(self, n):
# 		"""
# 		:type n: int
# 		:rtype: int
# 		"""
# 		left, right = 1, n
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			api_res = guess(mid)
# 			if api_res == 0:
# 				return mid
# 			if api_res == 1:
# 				left = mid
# 			else:
# 				right = mid
# 		return left if guess(left) == 0 else right
```

## 69. Sqrt(x) (easy)

简述：求根号x，取整

思路：从正确答案后，平方大于x

联系：出自278

```python
# class Solution(object):
# 	def mySqrt(self, x):
# 		"""
# 		:type x: int
# 		:rtype: int
# 		"""
# 		left, right = 1, x
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if mid * mid == x:
# 				return mid
# 			if mid * mid > x:
# 				right = mid
# 			else:
# 				left = mid
# 		return right if right * right <= x else left
```

## 302. Smallest Rectangle Enclosing Black Pixels (hard)

简述：二值化图片，黑色像素相连，给一个黑色像素坐标，求包围黑色区域的最小矩形面积

思路：从黑色坐标向上下左右找边界，一旦超过边界将再无黑色行列

联系：出自278

```python
# class Solution(object):
# 	def minArea(self, image, x, y):
# 		"""
# 		:type image: List[List[str]]
# 		:type x: int
# 		:type y: int
# 		:rtype: int
# 		"""
# 		m, n = len(image), len(image[0])
# 		def row_has_black(row):
# 			for j in range(n):
# 				if image[row][j] == '1':
# 					return True
# 			return False
		
# 		def col_has_black(col):
# 			for i in range(m):
# 				if image[i][col] == '1':
# 					return True
# 			return False
		
		def find_upper():
# 			left, right = 0, x
# 			while left + 1 < right:
# 				mid = (left + right) // 2
# 				if row_has_black(mid):
# 					right = mid
# 				else:
# 					left = mid
# 			if row_has_black(left):
# 				return left
# 			return right
		
		def find_lower():
# 			left, right = x, m - 1
# 			while left + 1 < right:
# 				mid = (left + right) // 2
# 				if row_has_black(mid):
# 					left = mid
# 				else:
# 					right = mid
# 			if row_has_black(right):
# 				return right
# 			return left
		
		def find_left():
# 			left, right = 0, y
# 			while left + 1 < right:
# 				mid = (left + right) // 2
# 				if col_has_black(mid):
# 					right = mid
# 				else:
# 					left = mid
# 			if col_has_black(left):
# 				return left
# 			return right
		
		def find_right():
# 			left, right = y, n - 1
# 			while left + 1 < right:
# 				mid = (left + right) // 2
# 				if col_has_black(mid):
# 					left = mid
# 				else:
# 					right = mid
# 			if col_has_black(right):
# 				return right
# 			return left
		
# 		upper, lower, left, right = find_upper(), find_lower(), find_left(), find_right()
		
		return (lower - upper + 1) * (right - left + 1)
```

# 二分答案

## 274. H-Index (medium)

简述：引用数组，求hIndex--h篇每篇至少h引用，其余每篇不超过h引用

思路：二分答案，答案经过一次状态改变

联系：

```python
# class Solution(object):
# 	def hIndex(self, citations):
# 		"""
# 		:type citations: List[int]
# 		:rtype: int
# 		"""
# 		if not citations:
# 			return 0
# 		left, right = 0, len(citations)
# 		while left + 1 < right:
# 			h = (left + right) // 2
			cnt = sum([x >= h for x in citations])
# 			if cnt == h:
# 				return h
# 			if cnt > h:
# 				left = h
# 			else:
# 				right = h
# 		cnt = sum([x >= right for x in citations])
# 		if cnt >= right:
# 			return right
# 		return left
```

## 275. H-Index II (hard)

简述：引用数组(已排序)，求hIndex--h篇每篇至少h引用，其余每篇不超过h引用

思路：不是二分答案，注意结尾情况

联系：出自274，但不属于二分答案

```python
# class Solution(object):
# 	def hIndex(self, citations):
# 		"""
# 		:type citations: List[int]
# 		:rtype: int
# 		"""
# 		if not citations:
# 			return 0
# 		N = len(citations)
# 		left, right = 0, N - 1
# 		while left + 1 < right:
# 			mid = (left + right) // 2
# 			if N - mid >= citations[mid]:
# 				left = mid
# 			else:
# 				right = mid

		# 要点1-1: 结尾情况很难想全        
		if N - right >= citations[right]:
			return citations[right]
		if N - right >= citations[left]:
			return N - right
		if N - left >= citations[left]:
			return citations[left]
		return N - left
```



































