---
layout: post
title: "leetcode刷题之Reverse Integer"
subtitle: '整数反转'
date:   2018-06-20 12:00:00
author: "hischen"
header-style: text
tags:
  - leetcode
---

题目：Given a 32-bit signed integer, reverse digits of an integer.

Example 1:

    Input: 123
    Output: 321
Example 2:

    Input: -123
    Output: -321
Example 3:

    Input: 120
    Output: 21

思路：

- 如何将数字反转。我们很容易想到使用一个栈来把数字挨个压入栈低，再从栈顶依次弹出。我们可以吃用下面的栈来满足我们的要求：   
    *出栈操作(pop)：*
    ```c
    pop = x % 10;
    x /= 10;
    ```
    *入栈操作(push):*
    ```c
    temp = output * 10 + pop;
    output = temp;
    ```
    

- 反转的时候如何保证不溢出

```c
public:
    int reverse(int x) {
    if ((x>pow(2,31)-1) || (x<pow(2,31)*(-1)) || x==0)
        return 0;
    int output=0;
    int temp;

    while(x!=0)
    {
        temp=x%10;
        x=x/10;
        if (output*10+temp>INT_MAX)
            return 0;
        if (output*10+temp<INT_MIN)
            return 0;
        output=output*10+temp;
    }
    return output;
    }
};
```
没有考虑32位整形能表示的最大数的溢出问题，我们得到下面的错误：

>Line 13: Char 19: runtime error: signed integer overflow: 964632435 * 10 cannot be represented in type 'int' (solution.cpp)
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior prog_joined.cpp:23:19

```c
class Solution {
public:
    int reverse(int x) {
    if ((x>pow(2,31)-1) || (x<pow(2,31)*(-1)) || x==0)
        return 0;
    int output=0;
    int temp;

    while(x!=0)
    {
        temp=x%10;
        x=x/10;
        if ((output*10>INT_MAX) || (output==INT_MAX/10) && (temp>INT_MAX%10))
            return 0;
        if ((output*10<INT_MIN) || (output==INT_MIN/10) && (temp<INT_MIN%10))
            return 0;
        output=output*10+temp;
    }
    return output;
    }
};
```


>Line 13: Char 20: runtime error: signed integer overflow: 964632435 * 10 cannot be represented in type 'int' (solution.cpp)
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior prog_joined.cpp:23:20

```c
class Solution {
public:
    int reverse(int x) {
    if ((x>pow(2,31)-1) || (x<pow(2,31)*(-1)) || x==0)
        return 0;
    int output=0;
    int temp;

    while(x!=0)
    {
        temp=x%10;
        x=x/10;
        if ((output>INT_MAX/10) || (output==INT_MAX/10) && (temp>INT_MAX%10))
            return 0;
        if ((output*10<INT_MIN/10) || (output==INT_MIN/10) && (temp<INT_MIN%10))
            return 0;
        output=output*10+temp;
    }
    return output;
    }
};

```
>输入:
-2147483412
输出
0
预期结果
-2143847412

```c
public:
    int reverse(int x) {
    if ((x>pow(2,31)-1) || (x<pow(2,31)*(-1)) || x==0)
        return 0;
    int output=0;
    int temp;

    while(x!=0)
    {
        temp=x%10;
        x=x/10;
        if ((output>INT_MAX/10) || (output==INT_MAX/10) && (temp>INT_MAX%10))
            return 0;
        if ((output<INT_MIN/10) || (output==INT_MIN/10) && (temp<INT_MIN%10))
            return 0;
        output=output*10+temp;
    }
    return output;
    }
};
```

复杂度分析

时间复杂度：O(log(x))，x 中大约有 log_{10}(x)位数字

空间复杂度：O(1)

