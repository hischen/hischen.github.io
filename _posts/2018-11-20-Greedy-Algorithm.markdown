---
layout:     post
title:      "Algorithm Design and Analysis--Greedy Algorithm"
subtitle:   "贪心"
date:       2018-11-20 12:00:00
author:     "hischen"
header-img: "img/post-namc.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Algorithm Design and Analysis
    - Greedy Algorithm
    - 贪心
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

>这学期选了卜老师的Algorithm Design and Analysis课，简单的分析一下Greedy Algorithm课后留的题目

1 Greedy Algorithm
-------------

&ensp;&ensp;Given two strings s and t, check if s is subsequence of t ?
A subsequence of a string is a new string which is formed from the original
string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ace" is a subsequence of
"abcde" while "aec" is not).


### 分析：
&ensp;&ensp;&ensp;&ensp;在这个问题中，我们要判断字符串s是否是字符串t的子序列。我们可以先分别求出s的长度m和t的长度n。定义一个变量k，表示字符串s和字符串t中相等元素的个数。然后从头到尾开始扫描两个字符串，如果一旦发现s和t某一位相等，那么变量k就加1。扫描完成以后，如果字符串s和字符串t中相等元素的个数k等于字符串s的长度m，那么我们可以判断s是t的子序列。相反的，如果我们最终得到的结果表明变量k不等于字符串s的长度m，则说明s不是字符串t的子序列. 

 
### pseudo-code:

```python
m=s.length                  #求字符串s的长度m
n=t.length							#求字符串t的长度n
bool subsequence(s,t,m,n)			
	k=0;
	for (i=0;k<m && i<n;i++)  #遍历两个字符串直到其中一个遍历完成
		if (s[k] == t[i])
			k=k+1;
	return (k==m);           #如果k等于m，则s是t的子序列
end

return min(AK,BK)
 ```   

### Prove the correctness：

- 在这个程序中，我们遍历两个字符串s和t，挨个比较t的当前元素和s的未匹配的元素，如果两者相等，匹配到了，则k的值加1。如果字符串s所有的元素都在字符串t中匹配到了，那么最后k一定是等于字符串s的长度m的。由此可以判断s是不是t的子序列。
- 当字符串s和字符串t的元素一个都匹配不到时，例如“AXY”和“BCD”则s必然不是t的子序列。此时，k的值将会为0，k不等于s的长度m。
- 当字符串s中的元素在字符串t中可以部分的匹配到时，例如“AXY”和“ABCD”，此时k的值将会小于s的长度m。s必然不是t的子序列。
- 当字符串s的长度m大于字符串t的长度n时，且t的元素可以在s中部分匹配到时，例如“AXYBC”和“AXY”，此时k的值将会小于s的长度m。s必然不是t的子序列。


### Analyse the complexity of your algorithm：
&ensp;&ensp;&ensp;&ensp;在这个算法中，程序只需要遍历字符串s和字符串t中较短的一个即可，只需要线性的时间。因此时间复杂度为$O(n)$。


## 2 Greedy Algorithm

&ensp;&ensp;Given a rope whose length is n, please cut the rope to m parts to get the
maximum product of the length of each part $\prod_{l1+l2+ \ldots+l_m=n}l_1*l_2*\ldots*l_m$.
  
&ensp;&ensp;For example, if a rope with length 8, when we cut it to 2, 3, 3, we can get the maximum product 18.


### 分析：
&ensp;&ensp;在这个问题中，我们要想得到最大的乘积，就要尝试在不同的位置切断绳子，然后比较不同位置切断时的最大值，求出所有可能的解，再取最大值即可。

### Describe the greedy-choice property and optimal substructure:  
&ensp;&ensp;&ensp;&ensp;最优子结构可以描述为:  
max_product(n)=max(i*(n-i),max_product(i*(n-i))&ensp;&ensp;&ensp;&ensp;i=1,2,3,…,n  
&ensp;&ensp;在这个问题中，我们将绳子切成i和n-i段，当总段数是两段时，得到的长度是i*(n-i)，当切成2段以上时，递归调用max_product函数得到max_product(i*(n-i))。将问题转化为求i*(n-i)和max_product(i*(n-i)当中较大的那个值，即为所要求的最大长度。



### pseudo-code：

```python
def	 max_product(n)：
	if(n==0):
		return 0;
	if (n==1):
		return 0;
	max_value=0;
	for i=1 to n-1:do
		max_value=max(max_value,i*(n-i),i*max_product(n-i)); #取最大值end for
	return max_value
```
</br>在上面的伪代码里，显然有部分子问题被重复计算了。在这里，我们可以把已经计算过的子问题保存在一张表里面，下次如果再求解该子问题则直接将表中的存放的值返回即可，这样避免了重复计算。更改后的伪代码如下：<br>

```python
def max_product(n):
		int value[];
		value[0]=0;
		value[1]=0;
		for i=1 to n :
			int max_value=0;
			for j=1 to i/2: do
				max_value=max(max_value,j*(i-j),j*value[i-j]);
			end for
			val[i]=max_value;
		return value[n]
```
### Prove the correctness：
&ensp;&ensp;&ensp;&ensp;在原有的伪代码的基础上加入了对表格中该单元的判断，如果该单元中有值，则表明已经计算过该子问题则直接返回，否则对该子问题进行求解并保存在表格中。


### Analyse the complexity：
&ensp;&ensp;改进后的伪代码中有两个循环，因此时间复杂度为$O(n^2)$。
