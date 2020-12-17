---
layout: post
title: "一些算法题目"
subtitle: '算法题目整理'
date:   2020-08-05 12:00:00
author: "hischen"
header-style: text
catalog:    true
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

# 1.题目：
-------------

给你一个长度为n的序列$A_1,A_2,...A_n$。
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

## **思路**:      
   考虑到要得到前后项的差要最大，那每一步的$B_i$要么取最小值1，要么取最大值$A_i$。
   用dp[i]表示第i步得到的最大和，则：    
$dp[i][0]$表示当第i步取最小值$1$时，前i项的最大和；
$dp[i][1]$表示当第i步取最大值$A_i$时，前i项的最大和；

初始状态有：


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


## 复杂度分析

时间复杂度：$O(n)$，需要遍历整个数组一次

空间复杂度：$O(n)$，需要开辟dp矩阵



2.连续子区间和 
-------------


题目描述：
给一串含有 c 个正整数的数组, 求出有多少个下标的连续区间, 它们的和大于等于 x。
解答要求：
时间限制：1000ms, 内存限制：64MB


输入：
第一行两个整数 c x（0 < c <= 1000000, 0 <= x <= 100000000)
第二行有 c 个正整数（每个正整数小于等于 100)。    

输出：
输出一个整数，表示所求的个数。   


输入样例1： 
``` 
5 6
2 5 3 8 4
```
输出样例：
```
11
```

输入样例2： 
``` 
3 6
2 4 7
```
输出样例：
```
4
```

## **思路**: 

1.直观的思路是使用双层循环暴力去求解，时间复杂度为$O(n^2)$


2.可以通过使用滑动窗口的方法来减小时间复杂度。滑动窗口的左侧边界为left，右侧边界为right。初始时，左右侧边界均为0，然后滑动右侧边界，直到窗口
内的和大于等于目标值停止，则right及right右边的值的个数均为所求解；此时将左侧边界向右滑动，继续求解。





```python
c,x=map(int,input().split())
nums=list(map(int,input().split()))
left,right=0,0
he=nums[right]
res=0
while left<c:
    while he<x and right<c-1:
        right=right+1
        he=he+nums[right]
    if he>=x:
        res=res+(c-1-right+1)
    he=he-nums[left]
    left=left+1
print(res)
```
牛客对应题目：https://www.nowcoder.com/questionTerminal/c7db49124acd415f801eb67de09c6d81


## 复杂度分析

时间复杂度：$O(n)$

空间复杂度：$O(1)$

3.拼接URL
-------------


题目描述：
给定一个URL前缀和URL后缀，需要将其连接为一个完整的URL，如果前缀结尾和后缀开头都没有“/”，需自动补上“/”连接符，如果前缀结尾和后缀开头都为“/”，需自动去重。

例如：  
1）前缀：/a；后缀:/b；连接后URL为:/a/b。  
2）前缀：/a/；后缀:/b；连接后URL为:/a/b。  
3）前缀：/a；后缀:b；连接后URL为:/a/b。  
4）前缀为空；后缀为空；连接后URL为：/。  


约束：不用考虑前后缀URL不合法情况。


输入描述：  
URL前缀（一个长度小于100的字符串），URL后缀（一个长度小于100的字符串）

输出描述：  
拼接后的URL。  


输入样例1： 
``` 
/a,/b
```
输出样例：
```
/a/b
```


## **思路**: 

将字符串s1后半部分的'/'都去掉，字符串s2前半部分的'/'都去掉，然后中间加上'/'拼接起来。  
```python
s1,s2=input().split(',')
if (not s1) and (not s2):
    print('/')
else:
    s1=s1.rstrip('/')
    s2=s2.lstrip('/')
    print(s1+'/'+s2)
print(res)
```


## 复杂度分析

时间复杂度：$O(n)$

空间复杂度：$O(1)$

4.捕鱼
-------------


题目描述：  
一款小游戏，有一个M X N的矩阵网格（M，N不大于100，左下角（0，N-1）放置在X/Y轴的起点，占据第一象限），每个网格上有K条小鱼（小鱼数量大于0，且小于100）；有一个形状为矩形，面积固定的（面积小鱼M*N），长宽不固定的渔网（整数单位，最小为1，长最大为N，最宽为M，与X轴平行为长，与Y轴平行为宽，左下角为起点），渔网的长宽可以随意调整，问要把渔网长宽设置为多少，起点设置在哪里？才能捕获最多的小鱼    
说明：如果渔网放置不同的点捕获的小鱼数量一致，则优先放置在距离原点（0，0）的直线距离最近的点；如果距离原点距离一致，则优先取x最小的点；X轴的第一个点表示0，Y轴的第一个点表示0       


输入描述：  
1、输入M，标识N行  
2、输入N，标识N列  
3、输入M x N的矩阵，每个单元的数字标识小鱼的数量  
4、输入渔网的面积  
3  
3  
1 2 3  
4 5 6  
7 8 9  
4

输出描述：  
渔网的X轴起点，渔网的Y轴起点；渔网的长，渔网的宽；捕获到的小鱼数量，例如：  
1,0;2,2;28    


输入样例1： 
``` 
5
5
1 2 3 7 9
4 5 6 8 9
7 8 9 8 2
9 9 6 8 9
7 8 9 6 2
9
```
输出样例：
```
0,0;3,3;72
```

输入样例2： 
``` 
3
3
1 2 3
4 5 6
7 8 9
4
```
输出样例：
```
1,0;2,2;28
```

## **思路**: 

将字符串s1后半部分的'/'都去掉，字符串s2前半部分的'/'都去掉，然后中间加上'/'拼接起来。  
```python
s1,s2=input().split(',')
if (not s1) and (not s2):
    print('/')
else:
    s1=s1.rstrip('/')
    s2=s2.lstrip('/')
    print(s1+'/'+s2)
print(res)
```


## 复杂度分析

时间复杂度：$O(n)$

空间复杂度：$O(1)$

5.删除有序链表中重复的元素
-------------

题目描述：  
删除给出链表中的重复元素（链表中元素从小到大有序），使链表中的所有元素都只出现一次    
例如：  
给出的链表为1->1->2,返回1->2.
给出的链表为1->1->2->3->3,返回1->2->3

输入样例1： 
``` 
{1,1,2}
```
输出样例：
```
{1,2}
```


## **思路**: 

从前往后依次遍历，遇到值重复的去重

```C++
class Solution {
public:
    ListNode *deleteDuplicates(ListNode *head) {
            if (head==nullptr){
            return nullptr;
        }
        ListNode *cur=head;
        while(cur){
            while (cur->next && cur->next->val==cur->val)
                {cur->next=cur->next->next;}
                cur=cur->next;
        }
        return head;       
    }
};
```


## 复杂度分析

时间复杂度：$O(n)$

空间复杂度：$O(1)$
