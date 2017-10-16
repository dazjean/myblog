---
title: javascript权威指南读书笔记—字符串
date: 2017-10-16 11:34:51
author: 张佳佳
tags:
	-JavaScript
---
# 字符串
> javascript采用utf-16编码unicode字符集，js字符串则由一组无序号的16位值组成的序列。

# 字符串长度
> 字符串长度length等于组成字符串的16位值unicode序列的个数。空字符串的长度为0。
> 一些不能使用16位unicode表示的字符也遵循utf-16的编码规则；用两个16位的unicode组成一个序列。所以长度length等于2的字符串有可能表示的是一个unicode字符

# 字符串索引
> 字符串索引和数组一样，索引以0开始表示第一个位置，通过charAt（index）可以访问某个索引上表示的字符。

# 字符串直接量
> 字符串直接量是单引号或者双引号括起来的字符序列。双引号或者单引号中可以包含单引号或者双引号
<!-- more -->
**特性**
- es3中规定字符串直接量必须在一行中，eg:
```
var s = 'hello world';
```
- 但在es5中支持多行，以‘\’反斜杠链接；eg:
```
var S = 'hello\
world'
```

# 字符串转义
> 在JavaScript中“\”反斜杠有着特殊的用途，通常反斜杠后面加上一个字符，就不再标识本身的含义了，比如。\n标识换行符。以下为JavaScript中定义的转义字符，如果\没有位于以下字符前，那么会忽略\
1. \0   nul字符 \u0000 
2. \b   退格符 "dfsfdsfsfdfsf"
3. \t   水平制表符
4. \n   换行符
5. \v   垂直制表符 "dfsfdsfsfdfsf"
6. \f   换页符
7. \r   回车符
8. \"   双引号
9. \'   单引号
10. \\   反斜杠
11. \xXX   由两位十六进制数XX组成的latin-1字符
12. \UXXXX  由四位十六进制数XXXX组成的unicode字符

# 字符串常用方法
```
var s = 'hello,word!'
s.charAr(0) //获取字符串第一个字符
s.substr(1,4)//获取字符串第一个字符到第5个字符
s.substring(1,4)// 截取字符串第2位到第四位的字符
s.slice(1,4) // 截取字符串第2位到第四位的字符 同上
s.slice(-3) // 负数倒叙，截取最后三位字符
s.split(',')// 分割成数组['hello','world!']
s.indexOf('l')//字符l首次出现的位置
s.lastIndexOf('l)//字符l最后一次出现的位置
s.replace('h',"H")//字符串匹配替换，全文
s.toUppperCase()//转化成大写字母
s.toLocaleLowerCase/toLowerCase //转化成小写，locale只有少数本地语言下有特定的大小写映射（土耳其）
```