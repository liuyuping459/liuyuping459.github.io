---
layout: post
title: "华为java机试题整理(2)"
date: 2015-06-29 13:20:00
author: liuyp
categories: java
tags: java
---

* content
{:toc}

##华为机试平台
听说华为有个机试平台，昨天用公共账号上去看了一下。发现上面的练习题比网上流传的测试题要明显难一些，很多高级和挑战级的题目还是涉及了很多方面的技术的，不熟悉的话，做题还真的要学一学。





做了两道相对简单的题，熟悉一下这个平台的使用。

###题1

* 在Stairs函数中实现该功能：
 * 一个楼梯有N阶，从下往上走，一步可以走一阶，也可以走两阶，有多少种走法？
 * 例如3阶楼梯有3种走法：
 * 1、1、1      1、2     2、1
 * 输入样例：3
 * 返回值样例： 3

这个题其实就类似一个斐波那契数列，在到达终点n之前，最后的一步，如果是上了一个台阶，这种情况的可能方案与终点是（n-1）的情况一样。如果是上了两个台阶，就与终点是（n-2）的情况一样。这样一来，n个台阶的方案就等于（n-1）个台阶和（n-2）个台阶方案的总和。
只是与斐波那契数列开头的几个数字不同，后面的算法是一样的。我们找到前几个就好了。可以用循环实现，当然也可以用递归。


		public class Test08 {
			public static void main(String[] args) {
				System.out.println(stairs(6));
			}
			public static long  stairs(int n)
			{
				long a = 0;
				long b = 1;
				long current = 0;
				
				for (int i = 1; i <= n; i++) {
					current = a + b;
					a = b;
					b = current;
				}
				return current;
			}
		}


###题2

 * @param inputStr
 *            输入字符串
 * @param outputStr
 *            输出判断结果
 * @description： 输入的inputStr是合法的IP，返回YES，否则返回NO 示例 输入：10.138.15.1 返回：YES

这个要注意 “.” 号要转义“\.”，不然就成了通配的符号，而且在java的String字符串中，"\"还有再加成“\\"，就成了”\\.“来匹配点号。
当然还要考虑ip地址的合理范围，比如0-255。还有127自环等就看情况了。


	public class Test09 {
		public void checkIP(String inputStr, StringBuffer outputStr) {		
			if (!inputStr.matches("[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}")) {
				outputStr.append("NO");
				return;
			}
			String[] array = inputStr.split("\\.");
			for (int i = 0; i < array.length; i++) {
				if (!(Integer.valueOf(array[i]) < 256)) {
					outputStr.append("NO");
					return;
				}
			}
			outputStr.append("YES");
		}
	}


##总结

还是前面说过的，这个平台上的题目还是要好好动脑子做的。如果有时间，可以尝试一个挑战级的题目。
上面的代码在[我的github仓库](https://github.com/liuyuping459/LearningDemo/tree/master/src/com/huawei)里的08和09练习。