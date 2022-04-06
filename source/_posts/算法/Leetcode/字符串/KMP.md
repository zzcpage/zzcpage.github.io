---
title: KMP算法
categories: 
    - 算法
    - 经典算法
tags: 
    - 算法
    - 字符串
---

- [1. 问题描述](#1-问题描述)
- [2. 解题思路](#2-解题思路)

### 1. 问题描述 


今天的问题很简单，就是给定两个字符串text和pattern，让你找出在text中是否存在一个序列等于pattern,如果存在就返回true,如果不存在就返回false 。

### 2. 解题思路
从题目描述上来看，很容易看出这题目是关于字符串匹配的问题，也就是从一段文本中，找出某个模式的全部出现位置的问题。

>1. 朴素的字符串匹配算法

用一个循环找出所有有效位移，对text每个字符进行检测，然后匹配pattern，如果匹配成功就返回下标。如果失败，就移动text下一个位置，重复操作。
```java
public int strStr(String haystack, String needle) {
        int len_h , len_s , i , j = 0 ;
        len_s = needle.length() ;
        if(len_s == 0 )
            return 0 ;
        len_h = haystack.length() ;
        for(i = 0 ; i <= len_h - len_s ; i++){
            if(haystack.charAt(i)==needle.charAt(j)){
                for(j=1;j<len_s;j++){
                    if(haystack.charAt(i+j)!=needle.charAt(j))
                        break ;
                }
                if(j>=len_s)
                    return i ;
                else
                    j = 0 ;
            }
        }

        return -1 ;
    }
```

>2. KMP算法

KMP算法的巧妙之处就是利用已经匹配字符串来确定下一次匹配开始的位置。这就是和朴素字符串匹配算法的区别，朴素字符串匹配算法每次匹配完毕之后，只是从下一个字符开始进行匹配，那么就浪费了已经匹配过字符串的有效信息，所以造成时间上的浪费。如何确定下一字符开始匹配的位置呢？假设已经匹配模式串pattern的位置为j,也就是从text的位置i开始 text[i.....,i+j] = pattern[0......,0+j]

>KMP算法的前缀函数(next)

前缀函数 $\pi$(i)(0 $\leq$ i < m)表示s 的子串 s[0:i] 的最长的相等的真前缀与真后缀的长度。特别地，如果不存在符合条件的前后缀，那么 $\pi$(i) = 0。其中真前缀与真后缀的定义为不等于自身的的前缀与后缀。

```javascript
/**
 * 求解字符前缀函数
 * /
 public int[] getNext(String s){
        int len , k ,q ;
        len = s.length() ;
        int[] next = new int[len];
        next[0] = 0 ;
        for(q = 1,k=0 ; q< len ; q++){  //长字符串的前缀建立在短字符前缀的匹配上。
            while (k>0&&s.charAt(k)!=s.charAt(q)){
                    k = next[k-1] ;
            }
            if(s.charAt(q) == s.charAt(k)){
                k = k+1 ;
            }
            next[q] = k ;
        }
        return next ;
    }
/**
 * 通过前缀函数进行字符串的匹配
 * /
    public int Matcher(String t , String p ){
        int   q , i;
        int n = t.length(), m = p.length();
        if (m == 0) {
            return 0;
        }
        int[] next = getNext(p) ;
        q = 0 ;
        for(i = 0 ,q=0; i < n ;i++){
            while(q>0&&p.charAt(q)!=t.charAt(i)){  //如果不匹配则移动到下一个位置
                q = next[q-1] ;
            }
            if(p.charAt(q) == t.charAt(i)){
                q = q+1 ;
            }
            if(q == m){
                return i-m+1 ;
            }
        }
        return -1 ;
    }

```

>个人总结

KMP算法主要在于计算前缀函数next，前缀函数的计算依据最长相同前后缀长度为依据来计算。通过next来获取到移动的位置。

对于next前缀函数的计算过程，patten(q) 和 patten(k) 不等说明了，在前一个（q-1）的最长前后缀匹配的位置，增加一个text(q)后，patten[0->next(q-1),next(q-1)+1] 和patten[(q-1)-next(q-1)->q]并不相等(因为新增的最后一个字符和前一个patten(q-1)字符串的最长前后缀并不相等)，所以需要进一步比较next[next(q-1)] 是否满足，patten[0->k+1] == patten[k-q,q] , 如果相等，则另k+1 , next[q] = k 。
![a6](/images/时间复杂度.PNG)