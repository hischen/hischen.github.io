---
layout: post
title: "leetcode刷题之Minimum Path Sum"
subtitle: '最小路径和'
date:   2019-02-20 12:00:00
author: "hischen"
header-style: text
tags:
  - leetcode
  - Minimum Path Sum
  - 最小路径和
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

题目：Given a _m_ x _n_ grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

**Note**: You can only move either down or right at any point in time.

Example:

    Input: 
    [
     [1,3,1],
        [1,5,1],
        [4,2,1]
    ]
    Output: 7
    Explanation: Because the path 1→3→1→1→1 minimizes the sum.


思路：       
　　很经典的一个动态规划问题，假设我们新建一个与原来一样大小的矩阵，然后令左上角的数字dp[0][0]为原矩阵的数字。然后从左上角遍历到到右下角（也可以从右下角遍历到左上角），记得判断遍历到矩阵时的边界条件就好。状态转移方程为：  

$$dp(i,j)=grid(i,j)+min(dp(i+1,j),dp(i,j+1))$$

　　在这里我们为了节约存储空间，就在原来的矩阵上存储，减小空间复杂度。代码如下：
```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        unsigned int m=grid.size();
        unsigned int n=grid[0].size();
        for(int i=grid.size()-1;i>=0;i--)
        {
            for(int j=grid[0].size()-1;j>=0;j--)
                {   
                    if ((i==(m-1))&&(j!=(n-1)))
                        grid[i][j]=grid[i][j]+grid[i][j+1];
                    if ((i!=(m-1))&&(j==(n-1)))
                        grid[i][j]=grid[i][j]+grid[i+1][j];
                    if ((i!=(m-1))&&(j!=(n-1)))
                        grid[i][j]=grid[i][j]+min(grid[i+1][j],grid[i][j+1]);
                }
        }
    return grid[0][0];
    }
};
```


复杂度分析

时间复杂度：_O(mn)_，需要遍历整个m*n矩阵一次

空间复杂度：_O(1)_，除了给定数组，不需要额外存储空间

