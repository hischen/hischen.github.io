---
layout:     post
title:      "Algorithm Design and Analysis--Linear Programming"
subtitle:   "线性规划"
date:       2018-12-19 12:00:00
author:     "hischen"
header-img: "img/post-namc.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Algorithm Design and Analysis
    - Linear Programming
    - 线性规划
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

>这学期选了卜老师的Algorithm Design and Analysis课，简单的分析一下Linear Programming课后留的题目

1 Linear-inequality feasibility
-------------

&ensp;&ensp;Given a set of m linear inequalities on n variables x1, x2,…,xn, the linear-inequality feasibility problem asks if there is a setting of the variables that simultaneously satisfies each of the inequalities.  
&ensp;&ensp;Show that if we have an algorithm for linear programming, we can use it to solve the linear-inequality feasibility problem. The number of variables and constraints that you use in the linear-programming problem should be polynomial in n and m.  

### 分析：
&emsp;&emsp;假设我们对如下线性规划的问题已经找到了解法：    

$$ max\sum_{j=1}^{n} {c_jx_j}$$
$$ \sum_{j=1}^{n} {a_{ij}\leq b_i},i=1,2,\cdots,m$$
$$ x_j \geq0,j=1,2,\cdots,n$$
&emsp;&emsp;我们可以令目标函数为：      

$$ max 0$$
&emsp;&emsp;然后求解这个线性规划问题，即可得到这个线性不等式问题的解。
## 2 Interval Scheduling Problem

&ensp;&ensp;A teaching building has m classrooms in total, and n courses are trying to use them. Each course $i( i = 1,2,\cdots,n)$only uses one classroom during time interval $[S_i, F_i] (F_i > S_i > 0)$. Considering any two courses can not be carried on in a same classroom at any time, you have to select as many courses as possible and arrange them without any time collision. For simplicity, suppose $2n$ elements in the set$ $｛S_1,F_1,\cdots, S_n , F_n ｝$are all different.  
&ensp;&ensp;Please use ILP to solve this problem, then construct an instance and use GLPK or Gurobi or other similar tools to solve it.


### 分析：
&emsp;&emsp;对于这个问题，我们将该问题转化为线性规划问题。我们可以假设$x_{ij}$为课程i占用编号为j的教室。$x_{ij}$为布尔类型变量，只能取1或者0。当课程$i$占用$j$教室时，$x_{ij}$为1，否则为0。即：  

$$x_{ij}=１　　当课程i占用j教室时$$
$$x_{ij}=0　　其他情况$$　　
其中，$i=1,2,\cdots,n;j=1,2,\cdots,m$。  
为了让尽可能多的课程被安排到，我们的目标函数可以设为：

$$MAX \sum_{i=1}^{n}\sum_{j=1}^{m}x_{ij}$$

约束条件有两个：
1.	一门课程只能安排一个教室，不能出现一门课程安排多个教室的情况。对应的约束条件为：

$$ \sum_{j=1}^{m}x_{ij}\leq1,i=1,2,\cdots,n$$
2.	如果两门课要安排在同一个教室，则他们的时间不能冲突。对应的约束条件为: 

$$ F_i(x_{ij}+x_{kj}-1)\leq S_k,0<i<k\leq n 　j=1,2,\cdots,m$$ 

当$x_{ij}$=$x_{kj}$=1时，课程$i$和课程$k$都要占用编号为$j$的教室，此时要想它们之前不发生冲突，则需要$F_i<=S_k$;当$x_{ij}$=1，$x_{kj}$=0 或者$x_{ij}$=0，$x_{kj}$=1时，也不会发生冲突。  

总的线性规划表达为：  

$$MAX \sum_{i=1}^{n}\sum_{j=1}^{m}x_{ij}$$  
$$ s.t.　F_i(x_{ij}+x_{kj}-1)\leq S_k,0<i<k\leq n 　j=1,2,\cdots,m$$ 
$$ \sum_{j=1}^{m}x_{ij}\leq1,i=1,2,\cdots,n$$
$$x_{ij}=0　or　１,i=1,2,\cdots,n;j=1,2,\cdots,m $$


在这里，我们假设共有2个教室，m=2，共有4节课，n=4。四节课每节课对应的上课时间的区间$[S_i,F_i]$为：[1,40],[1,40],[50,90],[50,90]。对应的GLPK的mod文件内容如下：


```bash
/* Variables */
var x11 binary;
var x12 binary;
var x21 binary;
var x22 binary;
var x31 binary;
var x32 binary;
var x41 binary;
var x42 binary;

/* Object function */
maximize z: x11+x12+x21+x22+x31+x32+x41+x42;

/* Constrains */
s.t. con1: 40*x11+40*x21-40 <= 1;
s.t. con2: 40*x11+40*x31-40 <= 50;
s.t. con3: 40*x11+40*x41-40 <= 50;
s.t. con4: 40*x21+40*x31-40 <= 50;
s.t. con5: 40*x21+40*x41-40 <= 50;
s.t. con6: 90*x31+90*x41-90 <= 50;
s.t. con7: 40*x12+40*x22-40 <= 1;
s.t. con8: 40*x12+40*x32-40 <= 50;
s.t. con9: 40*x12+40*x42-40 <= 50;
s.t. con10: 40*x22+40*x32-40 <= 50;
s.t. con11: 40*x22+40*x42-40 <= 50;
s.t. con12: 90*x32+90*x42-90 <= 50;
s.t. con13: x11+x12 <= 1;
s.t. con14: x21+x22 <= 1;
s.t. con15: x31+x32 <= 1;
s.t. con16: x41+x42 <= 1;
end;
```
执行如下命令:
```bash
glpsol -m Interval_Scheduling.mod -o Interval_Scheduling.sol
```

得到的Interval_Scheduling.sol内容如下：
```bash
Problem:    Interval_Scheduling
Rows:       17
Columns:    8 (8 integer, 8 binary)
Non-zeros:  40
Status:     INTEGER OPTIMAL
Objective:  z = 4 (MAXimum)

No.   Row name        Activity     Lower bound   Upper bound
------ ------------    ------------- ------------- -------------
  1 z                           4
  2 con1                       40                          41
  3 con2                       80                          90
  4 con3                       40                          90
  5 con4                       40                          90
  6 con5                        0                          90
  7 con6                       90                         140
  8 con7                       40                          41
  9 con8                        0                          90
 10 con9                       40                          90
 11 con10                      40                          90
 12 con11                      80                          90
 13 con12                      90                         140
 14 con13                       1                           1
 15 con14                       1                           1
 16 con15                       1                           1
 17 con16                       1                           1

No. Column name       Activity     Lower bound   Upper bound
------ ------------    ------------- ------------- -------------
 1  x11          *              1             0             1
 2  x12          *              0             0             1
 3  x21          *              0             0             1
 4  x22          *              1             0             1
 5  x31          *              1             0             1
 6  x32          *              0             0             1
 7  x41          *              0             0             1
 8  x42          *              1             0             1

Integer feasibility conditions:

KKT.PE: max.abs.err = 0.00e+00 on row 0
        max.rel.err = 0.00e+00 on row 0
        High quality

KKT.PB: max.abs.err = 0.00e+00 on row 0
        max.rel.err = 0.00e+00 on row 0
        High quality

End of output
```

根据得到的结果，$x_{11}=1,x_{12}=0,x_{21}=0,x_{22}=1,x_{31}=1,x_{32}=0,x_{41}=0,x_{42}=1$。即：将第一节课安排在教室一，将第二节课安排在教室二，将第三节课安排在教室一，将第四节课安排在教室二。

## 3 Gas Station Placement

&ensp;&ensp;Let's consider a long, quiet country road with towns scattered very sparsely along it. Sinopec, largest oil refiner in China, wants to place gas stations along
the road. Each gas station is assigned to a nearby town, and the distance between any two gas stations being as small as possible. Suppose there are n towns with distances from one endpoint of the road being $d_1,d_2,\cdots, d_n$. $n$ gas stations are to be placed along the road, one station for one town. Besides, each station is at most r far away from its correspond town. $d_1,\cdots, d_n$ and $r$ have been given and satisfied $d_1 < d_2 <\cdots< d_n, 0 < r < d_1$ and $d_i + r < d_{i+1}-r$ for all $i$. The objective is to find the optimal placement such that the maximal distance between two successive gas stations is minimized.  
&ensp;&ensp;Please formulate this problem as an LP.

### 分析：
&ensp;&ensp;&ensp;&ensp;对于这个问题，我们假设z为两个相邻的加气站的最大距离，$x_i$为第$i$个加气站距离路的端点的距离，$d_i$为第$i$个城镇距离路的端点的距离，$r$为每个城镇与对应的加气站之间的最大距离。我们可以列出如下的线性规划： 

$$min z$$  
$$s.t.　x_{i+1}-x_i\leq z　 i=2,3,\cdots,n$$  
$$d_i-r\leq x_i\leq d_i+r　i=1,2,\cdots,n$$  
$$d_i + r < d_{i+1}-r　i=1,\cdots,n-1$$  
$$0 < r < d_1$$  
$$d_1 < d_2 < \cdots < d_n$$  
$$x_i,z>=0$$  
转化为线性规划的标准型：  

$$min z$$
$$s.t.　x_{i+1}-x_i-z\leq 0　i=2,3,…,n$$
$$x_i<=d_i+r　i=1,2,…,n$$
$$- x_i \leq d_i-r　i=1,2,…,n$$
$$xi,z \geq 0$$

