---
layout: post
title: "leetcode刷题之Roman to Integer"
subtitle: '罗马数字转整数'
date:   2018-08-13 12:00:00
author: "hischen"
header-style: text
tags:
  - leetcode
  - Roman to Integer
  - 罗马数字转整数
---

## 题目：
Roman numerals are represented by seven different symbols: I,V,X,L,C,D and M.

```
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, two is written as II in Roman numeral, just two one's added together. Twelve is written as, XII, which is simply X + II. The number twenty seven is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

- I can be placed before V (5) and X (10) to make 4 and 9. 
- X can be placed before L (50) and C (100) to make 40 and 90. 
- C can be placed before D (500) and M (1000) to make 400 and 900.  

Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.


Example 1:

    Input: "III"
    Output: 3
Example 2:

    Input: "IV
    Output: 4
Example 3:

    Input: "IX"
    Output: 9
Example 4:

    Input: "LVIII"
    Output: 58
    Explanation: L = 50, V= 5, III = 3.
Example 5:

    Input: "MCMXCIV"
    Output: 1994
    Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.

## 思路：
　　对于这个问题，我们可以用一个字典将对应的字符和数值存储起来。然后从左到右遍历整个罗马数字的字符串，如果第i个罗马数字对应的数值比后面第i+1个要小，那么就减去这个数值，否则就加上这个数值。最后得到的结果就是答案。
　　

Python代码：  

```py
class Solution:
    def romanToInt(self, s: str) -> int:
        corresponding={'I':1,"V":5,"X":10,"L":50,"C":100,"D":500,"M":1000}
        result=0
        for i in range(len(s)):
            print(i)
            if ((i<(len(s)-1)) and (corresponding[s[i]]<corresponding[s[i+1]])):
                result=result-corresponding[s[i]]
            else:
                result=result+corresponding[s[i]]
        return result
```

C++代码：

```C++
{% raw %}
class Solution {
public:
    int romanToInt(string s) {
    unordered_map<char,int> corresponding={{'I',1},{'V',5},{'X',10},{'L',50},{'C',100},{'D',500},{'M',1000}};
    //cout<<(corresponding.at("M"));
    unsigned int l=s.size();
    int result=0;
    for(int i=0;i<l;i++)
    {
        //cout << typeid(s[i]).name()<<endl;
        if ((i<l-1)&&(corresponding.at(s[i])<corresponding.at(s[i+1])))
            result=result-corresponding.at(s[i]);
        else
            result=result+corresponding.at(s[i]);
    }
    return result;
    }
};
{% endraw %}
```

## 复杂度分析

时间复杂度：O(n)  
空间复杂度：O(1)

