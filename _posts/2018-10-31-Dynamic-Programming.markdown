---
layout:     post
title:      "Algorithm Design and Analysis--Dynamic Programming"
subtitle:   "动态规划"
date:       2018-10-31 12:00:00
author:     "hischen"
header-img: "img/post-namc.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Algorithm Design and Analysis
    - Dynamic Programming
    - 动态规划
---

>这学期选了卜老师的Algorithm Design and Analysis课，简单的分析一下课后留的题目

1 Money robbing
-------------

A robber is planning to rob houses along a street. Each house has a certain amount of money
stashed, the only constraint stopping you from robbing each of them is that adjacent houses
have security system connected and it will automatically contact the police if two adjacent
houses were broken into on the same night.

1. Given a list of non-negative integers representing the amount of money of each house,
     determine the maximum amount of money you can rob tonight without alerting the
       police.

2. What if all houses are arranged in a circle?

分析：在这个问题中，我们设街道上一共有n间房子，dp[i]表示第i个房子能抢到的最大值，第i个房子的钱为values[i]。对于第i个房子d[i]，如果选择抢，则上一个房子不能抢，抢劫者得到的钱为dp[i-2]+value[i]；如果选择不抢，则得到的钱仍然是在上一个房子抢到的钱不变，为dp[i-1]。因此，我们可以得到如下的递推表达式： 

    
    DP  equation：dp[i]=max(dp[i-2]+value[i],dp[i-1])
    

   如果所有的房子排列成一个圆圈，则说明第一个房子和最后一个房子不能同时抢。这时，我们可以同时计算第1个房子到第n-1个房子能抢到的最大值和第2个房子到第n个房子能抢到的最大值(避免第1个房子和第n个房子同时被抢的情况），然后取其中的最大值即可。

   pseudo-code(not circle)：

   ```python
   input:a sequence of certain amount of money values[i],i from 0 to n-1
   output: the maximum amount of money robber can rob
   def maximum_rob(values):
       n=len(values)           
       if n=1:
           return values[0]
       if n=2:
           return max(values[0],value[1])
       dp[0]=values[0]
       dp[1]=max(values[0],values[1])
       for i=2 to n:
           dp[i]=max(dp[i-2]+value[i],dp[i-1])
       return dp[n-1]
   
   ```

pseudo-code(circle)：

   ```python
   input:a sequence of certain amount of money values[i],i from 0 to n-1
   output: the maximum amount of money robber can rob
   def maximum_rob_circle(values):
   	n=len(values)
       if n=1:
           return values[0]
       if n=2:
           return max(values[0],value[1])
       return max(maximum_rob(values[0:n-2]),maximum_rob(values[1:n-1]))
   ```


   Prove the correctness：

   ​	对算法的正确性证明在于对状态转移方程的正确性证明，如果只有一间房子，那么抢到的最大值就是这间房子；如果只有两间房子，那么抢到的应该是这两间房子中value较大钱较多的房子；如果n大于等于3，对于第i个房子，如果不抢，显然能抢到的最大值dp[i]为dp[i-1]；如果抢，则显然抢到的最大值为dp[i]=dp[i-2]+value[i]，我们只需要在对每一个房屋选择时选择两个其中最大的值即可。

   ​     	当房子呈圆形排列时，总问题可以划分为两个子问题。因为第一个房屋和最后一个房屋首位相连，抢劫者不能同时抢劫者两个房屋。这样，我们可以将原始问题划分为两个子问题：包括都一个房屋而不包括最后一个房屋的子问题，不包括第一个房屋而包括最后一个房屋的子问题。在最后，我们可以比较这两个规模为n-1的子问题的结果，选择较大的那个即为最终要求的结果。

   Analyse the complexity：

   对于房子不是圆形排列的情况，我们只遍历了一次数组，因此时间复杂度为O(n)。

   对于房子圆形排列的情况，我们将原始问题划分为两个规模为n-1的子问题，遍历了两次数组，并进行了一次比较。T(n)=2T(n-1)+1，时间复杂度仍为O(n)。

## 2 Node selection

You are given a binary tree, and each node in the tree has a positive integer weight. If you
select a node, then its children and parent nodes cannot be selected. Your task is to find a set of nodes, which has the maximum sum of weight.

分析：对于这个问题，我们可以考虑二叉树的根节点：

（1）当根节点被加入set(集合)时，那么它的孩子节点都不能被加入集合，只能加入根节点的孙子节点，再加入孙子节点的孙子节点，对孙子节点不断的递归，计入set集合，直到到达二叉树的叶子节点结束递归，计算set集合的和。

    
    level1=sum(root+root.grandchild+... ...)
    
（2）当根节点没有被加入set集合时，那么它的左右孩子节点都要被加入集合中，它的孩子节点的孙子节点也要被向下递归的加入set集合中，直到到达二叉树的叶子节点结束递归。在这种情况下，我们可以将原始问题分解为与第（1）种情况相同的两个子问题。将根节点的左右孩子节点视为根节点，按照根节点被加入set(集合)时的情况进行考虑，最后将两个子问题，即左右孩子节点递归得到的值加起来，即为第二种情况的set集合的值。
	
    
    level2=sum(root.left+sum.right+root.left.grandchild+root.right.grandchild+... ...)
    

​	当分别得到第一种情况和第二种情况的set集合的和时，我们比较得到两个和的大小，取较大的和对应的set集合即为所求。

    maximum.sum=max(level1,level2)


​	如下面的二叉树所示，如果我们选择了第一层加入集合（选择根节点），那么我们必须选择第三层，第五层，......奇数层加入集合。同样的，如果我们选择了第二层（不选择根节点），那么我们必须选择第四层，第六层，......偶数层加入集合。对于上图所示的二叉树，我们可以选择加入的节点有（1，4，5，6，12，13，14）或（2，3，7，8，9，10，11）。它们的和的最大值分别为55和50，因此对于这个二叉树，应该选择的集合是第一层，第三层，第五层的所有节点。

```
 			 1
           /   \
          2     3
        /      /  \
      4       5     6
    /  \     /     / \ 
   7  	8   9    10   11
 /     /  \
12    13   14 
```

pseudo-code：

```pseudocode
input:node root of a binary tree
output:a set of nodes, which has the maximum sum of weight.
def get_sum(node):
	if(node==NULL)
		return 0
	int sum=root.data
	if(root.left!=NULL)
		{
            sum=sum+get_sum(root.left.left)
            sum=sum+get_sum(root.left.right)
		}
	if(root.right!=NULL)
		{
            sum=sum+get_sum(root.right.left)
            sum=sum+get_sum(root.right.right)
		}
	return sum
def maximum_sum(root):
	if(root==NULL)
		return 0
	return max(get_sum(root),get_sum(root.left)+get_sum(root.right))
def result_set(root):
	if(maximum_sum(root)=get_sum(root)
		return set1  #include root node
	else return set2 #not include root node
```



Prove the correctness：

​	在考虑这个问题时，我们注意到，一个节点和它的孩子节点不能同时被加入集合中。所以当我们选择一个节点加入到集合当中时，我们必须递归地将它的孙子节点都加入到集合中。否则，当我们选择不加入这个节点时，我们将它的孩子节点加入到集合中去,然后必须递归地将它的孩子节点的孙子节点加入到集合中。然后比较两种情况下得到的和，如果是加入根节点对应的集合的和最大，则返回根节点和它的所有孙子节点；如果是不加入根节点对应的集合的和最大，则返回根节点的孩子节点和根节点的孩子节点的所有孙子节点。

Analyse the complexity：我们将原始问题划分为两种情况，遍历了两次节点数为n的二叉树，并进行了一次比较。时间复杂度仍为O(n)。

## 3 Decoding

A message containing letters from A-Z is being encoded to numbers using the following
mapping:
<center>A : 1</center>
<center>B : 2</center>
<center>: : :</center>
<center>Z : 26</center>
Given an encoded message containing digits, determine the total number of ways to decode
it.
For example, given encoded message “12”, it could be decoded as “AB” (1 2) or “L” (1 2).
The number of ways decoding “12”  is 2.

分析：对于这个问题，一个编码后的含有n个数字的数字串s，s的所有的字符都出现在0-9之间。对于这个数字串s，设dp[i]为s[0...i]最多的解码方式，要分析它的解码方式有多少种可能，主要考虑两种情况：

- 将所有的数字视为单个的字母时，当s[i]不等于0时，dp[i]=dp[i]+dp[i-1]

- 将s[i]和s[i-1]组合成一个字母的情况，而且组合成的数字必须大于等于10且小于等于26，才能有对应的字母与之对应，即：当i>=2且，10<=s[i-1]*10+s[i]<=26时，dp[i]=dp[i-2]+dp[i]

  pseudo-code：

  ```python
  input:an array s consists of n digits
  output:The number of ways decoding the array s
  def the number of decoding ways(s):
      n=s.length
      if s[0]!=0:
          dp[0]=1
      else dp[0]=0
      for i=1 to n-1:
          if (s[i-1]*10+s[i])>=10 && (s[i-1]*10+s[i])<=26:
              if i>=2:
              dp[i]=dp[i-2]+dp[i]
              else dp[i]=1
          if s[i]!=0:
                dp[i]=dp[i]+dp[i-1]
  	return dp[n-1]
  ```

  Prove the correctness：

  在这个问题中，我们需要考虑两种情况：

  - 对于当前位置s[i]，考虑单个数字的情况，如果s[i]不为0，则dp[i]=dp[i]+dp[i-1]；若果s[i]为0，则说明s[i]可以和上一个数字s[i-1]组成一个两位数。

  - 对于当前位置s[i]，考虑和上一个数字组成两位数的情况，如果能和上一个数字s[i-1]组合成一个数字，该数字的范围在[10,26]区间内，则说明解码方式可以为dp[i]=dp[i]+dp[i-2]。

    因此，算法的递推表达式正确无误。

  Analyse the complexity：

  ​	在这个算法中，我们只遍历了一次长度为n的数组s，因此时间复杂度为O(n)。

