---
layout: post
title: "最近遇到的一些算法题目"
subtitle: '算法题目整理'
date:   2020-08-05 12:00:00
author: "hischen"
header-style: text
tags:
  - 算法题目
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

**1**.题目：给你一个长度为n的序列$A_1,A_2,...A_n$。
然后构造一个长度为n的序列$B_1,B_2,...B_n$ ($1\leq i\leq n,1\leq B_i\leq A_i$)。
使 $\sum_{i=2}^{n} {\left| B_i-B_{i-1}\right|}$ 的值最大。

输入描述：
第一行包含一个整数n表示序列的长度。(1<=n<=50000)
第二行包含n个整数，分别表示$A_i(1<=i<=n,1<=A_i<=10000)$

输出描述：
输出最大值

输入样例： 
``` 
5  
10
1
10
1
10
```
输出样例：
```
36
```

**思路**:      
   考虑到要得到前后项的差要最大，那每一步的$B_i$要么取最小值1，要么取最大值$A_i$。
   用dp[i]表示第i步得到的最大和，则：    
$dp[i][0]$表示当第i步取最小值$1$时，前i项的最大和；
$dp[i][1]$表示当第i步取最大值$A_i$时，前i项的最大和；

初试状态有：


$$dp[0][0]=0$$


$$dp[0][1]=0$$

状态转移方程为：


$$dp[i][0]=max(dp[i-1][0],dp[i-1][1]+abs(1-a[i]))$$


$dp[i][1]=max(dp[i-1][0]+abs(a[i]-1),dp[i-1][1]+abs(a[i]-a[i-1]))$



```python
n=int(input())
a=[]
for _ in range(n):
    a.append(int(input()))
dp=[[0]*2 for _ in range(n)]
dp[0][0]=0
dp[0][1]=0
#print(dp)
for i in range(1,n):
    print(i)
    dp[i][0]=max(dp[i-1][0],dp[i-1][1]+abs(1-a[i]))
    dp[i][1]=max(dp[i-1][0]+abs(a[i]-1),dp[i-1][1]+abs(a[i]-a[i-1]))
print(max(dp[n-1][0],dp[n-1][1]))
```


复杂度分析

时间复杂度：$O(n)$，需要遍历整个数组一次

空间复杂度：$O(n)$，需要开辟dp矩阵

