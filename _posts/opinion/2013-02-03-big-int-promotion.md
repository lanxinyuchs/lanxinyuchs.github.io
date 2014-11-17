---
layout: post
title: C语言中的大数(big integer)提升
category: opinion
tags:
- C语言
---

###C语言中的大数(big integer)提升

第一个程序char.c
```  
unsigned char x = 2;  
signed char z = 5;  
if((x-z)<0)  
  printf("x-z<0\n");   
else if((x-z)>0)  
  printf("x-z>0\n");   
```
输出结果是 x-z<0
 
 
第二个程序int.c

    unsigned int x = 2;  
    signed int z = 5;  
    if((x-z)<0)  
      printf("x-z<0\n");   
    else if((x-z)>0)  
      printf("x-z>0\n"); 

输出结果是x-z>0
 
一般来说，无符号数和有符号数混合运算时，会自动转换为无符号数，那为什么数据类型定义成char和int会有不同的结果呢？这是因为K＆R C中关于整型提升(integral promotion)的定义为：
"A character, a short integer, or an integer bit-field, all either signed or not, or an object of enumeration type, may be used in an expression wherever an integer maybe used. If an int can represent all the values of the original type, then the value is converted to int; otherwise the value is converted to unsigned int. This process is called integral promotion."

[参考这篇文章](http://blog.csdn.net/lovekatherine/article/details/1565969)




