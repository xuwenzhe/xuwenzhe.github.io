# Leetcode：位运算


{{< figure src="/leetcode-bit-manipulation/mindmaster.png" class="center" width="500" >}}

# while位

## 342. Power of Four (easy)

简述：判断是否为4的幂

思路：mask挪位置

联系：出自693

```python
# class Solution(object):
# 	def isPowerOfFour(self, num):
# 		"""
# 		:type num: int
# 		:rtype: bool
# 		"""
# 		base = 1
		while base <= num:
# 			if base == num:
# 				return True
			# 要点1-1: 偶数位为1
			base <<= 2
# 		return False
```

## 476. Number Complement (easy)

简述：将所给数二进制取反输出对应十进制

思路：利用stack

联系：出自342

```python
# class Solution(object):
# 	def findComplement(self, num):
# 		"""
# 		:type num: int
# 		:rtype: int
# 		"""
# 		stack = []
# 		while num:
# 			stack.append(1 ^ (num & 1))
# 			num = num >> 1
# 		res = 0
# 		while stack:
			res = (res << 1) + stack.pop()
# 		return res
```

## 693. Binary Number with Alternating Bits (easy)

简述：判断所给数二进制是否间隔均不同，即`...0101...`或者`...1010...`

思路：while异常退出，逐位判断

联系：342

```python
# class Solution(object):
# 	def hasAlternatingBits(self, n):
# 		"""
# 		:type n: int
# 		:rtype: bool
# 		"""
# 		state = n & 1
# 		while n:
# 			n = n >> 1
			# 要点1-1: python位与，位xor
			if state ^ (n & 1) != 1:
# 				return False
# 			state ^= 1
# 		return True
```

## 477. Total Hamming Distance (medium)

简述：求所有数对hamming距离之和

思路：找出每一位位置上，多少个数为1或为0

联系：342

```python
# class Solution(object):
# 	def totalHammingDistance(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: int
# 		"""
# 		res = 0
# 		for i in range(32):
			countZeros = sum([x & (1<<i) == 0 for x in nums])
			res += countZeros * (len(nums) - countZeros)
# 		return res
```

## 201. Bitwise AND of Numbers Range (medium)

简述：求区间内所有数位AND值

思路：只要区间跨过1，末位AND值为0，递归求解

联系：

```python
# class Solution(object):
# 	def rangeBitwiseAnd(self, m, n):
# 		"""
# 		:type m: int
# 		:type n: int
# 		:rtype: int
# 		"""
# 		if m == n:
# 			return m
# 		if n == m + 1:
# 			return m & n
		# 要点1-1: 区间跨过1，末位AND值为0，递归
		return self.rangeBitwiseAnd(m >> 1, n >> 1) << 1
```

# 与关系

## 191. Number of 1 Bits (easy)

简述：求所给数二进制里多少位是1

思路：`n & (n-1)`去掉最后一位1

联系：

```python
# class Solution(object):
# 	def hammingWeight(self, n):
# 		"""
# 		:type n: int
# 		:rtype: int
# 		"""
# 		res = 0
# 		while n:
			# n & (n-1) 去掉最后一位1
			n = n & (n - 1)
# 			res += 1
# 		return res
```

## 231. Power of Two (easy)

简述：判断是否为2的幂

思路：二进制中有且仅有1个1

联系：出自191

```python
# class Solution(object):
# 	def isPowerOfTwo(self, n):
# 		"""
# 		:type n: int
# 		:rtype: bool
# 		"""
# 		return n & (n-1) == 0 and n != 0
```

## 461. Hamming Distance (easy)

简述：求所给数对的hamming distance

思路：先亦或，再数多少个1

联系：出自191

```python
# class Solution(object):
# 	def hammingDistance(self, x, y):
# 		"""
# 		:type x: int
# 		:type y: int
# 		:rtype: int
# 		"""
		num = x ^ y
# 		res = 0
# 		while num:
			num = num & (num - 1)
# 			res += 1
# 		return res
```

# 异或关系

## 136. Single Number (easy)

简述：数组中除某元素外每个元素出现两次，找出该元素

思路：碰碰对

联系：

```python
# class Solution(object):
# 	def singleNumber(self, nums):
# 		"""
# 		:type nums: List[int]
# 		:rtype: int
# 		"""
		res = 0
# 		for num in nums:
# 			res ^= num
# 		return res
```

## 389. Find the Difference (easy)

简述：字符串shuffle后添加一个字符，找出该字符

思路：碰碰对

联系：出自136

```python
# class Solution(object):
# 	def findTheDifference(self, s, t):
# 		"""
# 		:type s: str
# 		:type t: str
# 		:rtype: str
# 		"""
# 		res = 0
# 		# 要点1-1: python中ord与chr的使用
# 		for c in s:
# 			res = res ^ ord(c)
# 		for c in t:
# 			res = res ^ ord(c)
# 		return chr(res)
```





























































