---
layout: post
title: "华为校园招聘java机试题整理"
date: 2015-06-28 16:48:00
author: liuyp
categories: java
tags: java test
---

* content
{:toc}

## 华为校园招聘java机试题整理

这两周又在赶项目。我就一直在思考，你说建筑项目，像盖个楼，都是先设计好了，再施工，没有说楼盖一半了，要求加个房间的吧。这软件项目，不管你盖到第几层，想加就加，想拆就拆，跟搞机械的甲方沟通，真是不太好沟。

事实证明，掌握好设计原则，用好设计模式，对防范需求变化还是很有用的。

说正题，这几天西安华为要开始校园招聘，加了华为的招聘群，也很是热闹。周日没事了，在网上找了几道java的机试题，做完分享一下，有问题的地方也请指正。





###题1
如描述，不知道能不能用stack，就傻傻的自己写了一个。就是比较前后括号是否按规则成对出现。

```java
/**
	 * 检查输入字符串中括号是否匹配
	 * @author liu
	 *
	 */
	public class Test01 {
		public static void main(String[] args) {
			BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
			try {
				char[] clist= br.readLine().trim().toCharArray();
				Stack stack = new Stack();
				for(int i = 0; i < clist.length; i++) {
					if (clist[i] == '(' || clist[i] == '[') {
						stack.add(clist[i]);
					}
					else if (clist[i] == ')') {
						if (stack.get() != '(') {
							System.out.println("0");
							return;
						}
					}
					else if (clist[i] == ']') {
						if (stack.get() != '[') {
							System.out.println("0");
							return;
						}
					}
				}
				if (stack.isEmpty()) {
					System.out.println("1");
				}
				else {
					System.out.println("0");
				}
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
	
	class Stack {
		List<Character> list = new ArrayList<Character>();
		
		public void add(char c) {
			list.add(c);
		}
		
		public char get() {
			char c = list.get(list.size() - 1);
			list.remove(list.size() - 1);
			return c;
		}
		
		public boolean isEmpty() {
			return list.isEmpty();
		}
		
		public void show() {
			for(Character character : list) {
				System.out.println(character);
			}
		}
	}
```
	

###题2

```java
/**
	 * 第一题，输入字符串长度len1，字符串s1，字符串长度len2，字符串s2。
	 * 从后向前比较，以最短字符串为标准，输出不同的元素的个数。
	 * 例如:  输入：s1="1,3,5"   len1=3        s2="2,4,1,7,5"   len2=5  
	 * 		输出：2  
	 * 函数原型
	 * public int getDiffNum(int len1, String s1, int len2, String s2)
	 * @author liu
	 *
	 */
	public class Test02 {
		public static void main(String[] args) {
			Test02 test = new Test02();
			System.out.println(test.getDiffNum(3, "1,3,5", 5, "2,4,1,7,5"));
		}
		
		public int getDiffNum(int len1, String s1, int len2, String s2) {
			int num = 0;
			int[] array1 = getArray(s1, len1);
			int[] array2 = getArray(s2, len2);
			int k = len1 > len2 ? len2 : len1;
			for (int i = 0; i < k; i++) {
				if (array1[i] == array2[i]) {
					num+=1;
				}
			}
			
			return num;
		}
		
		private int[] getArray(String str, int len) {
			String[] array = new String[len];
			int[] intArray = new int[len];
			array = str.split(",");
			for (int i = 0; i < len; i++) {
				intArray[len - i - 1] = Integer.valueOf(array[i]);
			}
			return intArray;
		}
	}
```
	

###题3
就是表达式求值，如果熟悉逆波兰表达式，可以直接把中序表达式转换为后序表达式来做。但是不常用的肯定不会很熟悉，这个题要求又比较简单，没有括号，所以用双栈对倒的方法，思路比较简单。

就像我们人脑计算表达式一样，第一遍把乘除法算完，第二遍再算加减法。

```java
/**
	 *  第三题，输入一个表达式，没有括号，数字小于0-9之间，输出计算结果，
	 * 所有的中间结果化为整形。
	 *  例如:  输入：3+8×2/9-2  
	 *  输出：2  
	 *  函数原型 
	 *  public int getMyRet(String str)
	 * @author liu
	 *
	 */
	public class Test03 {
		public static void main(String[] args) {
			String str = "3+8*2/9-2";	// 382*9/+2- 
			Test03 test = new Test03();
			System.out.println(test.getMyRet(str));
		}
		
		public int getMyRet(String str) {
			int temp = 0;
			
			Stack<String> left = new Stack<String>();
			Stack<String> right = new Stack<String>();
			
			char[] c = str.toCharArray();
			for (int i = c.length - 1; i >= 0; i--) {
				right.add(String.valueOf(c[i]));
			}
			while(!right.isEmpty()) {
				if (isNum(right.peek())) {
					left.add(right.pop());
				}
				else if (right.peek().equals("*")) {
					right.pop();
					temp = Integer.valueOf(left.pop()) * Integer.valueOf(right.pop());
					left.add(String.valueOf(temp));
				}
				else if (right.peek().equals("/")) {
					right.pop();
					temp = Integer.valueOf(left.pop()) / Integer.valueOf(right.pop());
					left.add(String.valueOf(temp));
				}
				else {
					left.add(right.pop());
				}
			}
			while(!left.isEmpty()) {
				right.add(left.pop());
			}
			while(!right.isEmpty()) {
				if (isNum(right.peek())) {
					left.add(right.pop());
				}
				else if (right.peek().equals("+")) {
					right.pop();
					temp = Integer.valueOf(left.pop()) + Integer.valueOf(right.pop());
					left.add(String.valueOf(temp));
				}
				else if (right.peek().equals("-")) {
					right.pop();
					temp = Integer.valueOf(left.pop()) - Integer.valueOf(right.pop());
					left.add(String.valueOf(temp));
				}
			}
			
			return Integer.valueOf(left.pop());
		}
		
		private boolean isNum(String s) {
			return s.matches("[0-9]+");
		}
	}
```
	

###题4

```java
/**
	 * 1、选秀节目打分，分为专家评委和大众评委，score[] 数组里面存储每个评委打
	 * 的分数，judge_type[] 里存储与 score[] 数组
	 * 对应的评委类别，judge_type[i] == 1，表示专家评委，judge_type[i] == 2，
	 * 表示大众评委，n表示评委总数。打分规则如下：
	 * 专家评委和大众评委的分数先分别取一个平均分（平均分取整），然后，总分 = 专家
	 * 评委平均分  * 0.6 + 大众评委 * 0.4，总分取整。
	 * 如果没有大众评委，则 总分 = 专家评委平均分，总分取整。函数最终返回选手得分。
	 * @author liu
	 *
	 */
	public class Test04 {
		public static void main(String[] args) {
			int[] score = {20,30,40,50,60};
			int[] judge_type = {1,1,1,2,2};
			int n = 5;
			System.out.println(new Test04().judge(score, judge_type, n));
		}
		
		public int judge(int[] score, int[] judge_type, int n) {
			int average = 0;
			int expertScore = 0;
			int expertNum = 0;
			int publicScore = 0;
			int publicNum = 0;
			for (int i = 0; i < score.length; i++) {
				if (judge_type[i] == 1) {
					expertScore += score[i];
					expertNum += 1;
				}
				else if (judge_type[i] == 2) {
					publicScore += score[i];
					publicNum += 1;
				}
			}
			if (publicNum == 0) {
				average = expertScore / expertNum;
			}
			else {
				average = (int) (expertScore / expertNum * 0.6 + publicScore / publicNum * 0.4);
			}
			return average;
		}
	}
```
	

###题5

先排个序，然后先处理中间位置，再处理两边普通位置，最后处理最两边特殊位置。

```java
/**
	 * 2、给定一个数组input[] ，如果数组长度n为奇数，则将数组中最大的元素放到
	 *  output[] 数组最中间的位置，如果数组长度n为偶数，
	 * 则将数组中最大的元素放到 output[] 数组中间两个位置偏右的那个位置上，
	 * 然后再按从大到小的顺序，依次在第一个位置的两边，按照一
	 * 左一右的顺序，依次存放剩下的数。例如：input[] = {3, 6, 1, 9, 7}
	 *    output[] = {3, 7, 9, 6, 1};          
	 *    input[] = {3, 6, 1, 9, 7, 8}    output[] = {1, 6, 8, 9, 7, 3}
	 *    函数接口   void sort(int input[], int n, int output[])
	 * @author liu
	 *
	 */
	public class Test05 {
		public static void main(String[] args) {
			//int n = 5;
			//int input[] = {3, 6, 1, 9, 7};
			int n = 6;
			int input[] = {3,6,1,9,7,8};
	 		int output[] = new int[n];
			new Test05().sort(input, n, output);
			for (int i = 0 ; i < output.length; i++) {
				System.out.println(output[i]);
			}
		}
		
		public void sort(int input[], int n, int output[]) {
			bubbleSort(input);
			int k = n/2;
			output[k] = input[0];
			for (int i = 1; i < (n-1)/2 + 1; i++) {
				output[k - i] = input[2*i - 1];
				output[k + i] = input[2*i];
			}
			if (n%2 == 0) {
				output[0] = input[n - 1];
			}
		}
		
		private void bubbleSort(int[] array) {
			for (int i = 1; i < array.length; i++) {
				for (int k = 0; k < array.length - 1; k++) {
					if (array[k] < array[k + 1]) {
						int temp = array[k];
						array[k] = array[k + 1];
						array[k + 1] = temp;
					}
				}
			}
		}

	}
```
	


###题6

首先对任务分级进行排序，排序过程中要记录每个任务原来对应的位置。在分别按顺序加到相应的队列中。

```java
/**
	 * 3、操作系统任务调度问题。操作系统任务分为系统任务和用户任务两种。其中，
	 * 系统任务的优先级 < 50，用户任务的优先级 >= 50且 <= 255。
	 * 优先级大于255的为非法任务，应予以剔除。现有一任务队列task[]，长度为n，
	 * task中的元素值表示任务的优先级，数值越小，优先级越高。
	 * 函数scheduler实现如下功能，将task[] 中的任务按照系统任务、用户任务依次
	 * 存放到 system_task[] 数组和 user_task[] 数组中
	 * （数组中元素的值是任务在task[] 数组中的下标），并且优先级高的任务排在
	 * 前面，优先级相同的任务按照入队顺序排列（即先入队的任务排在
	 * 前面），数组元素为-1表示结束。例如：task[] = {0, 30, 155, 1, 80, 
	 * 300, 170, 40, 99}    
	 * system_task[] = {0, 3, 1, 7, -1}    
	 * user_task[] = {4, 8, 2, 6, -1}
	 * 
	 *  函数接口    
	 * void scheduler(int task[], int n, int system_task[], int user_task[])
	 * @author liu
	 *
	 */
	public class Test06 {
		public static void main(String[] args) {
			int task[] = {0, 30, 155, 1, 80, 300, 170, 40, 99};
			int system_task[] = new int[5];
			int user_task[] = new int[5];
			int n = 9;
			new Test06().scheduler(task, n, system_task, user_task);
			for (int i : system_task) {
				System.out.println(i);
			}
			for (int i : user_task) {
				System.out.println(i);
			}
		}
		
		void scheduler(int task[], int n, int system_task[], int user_task[]) {
			int[] index = sort(task);
			int s = 0;
			int u = 0;
			for (int i = 0; i < task.length; i++) {
				if (task[i] < 50) {
					system_task[s++] = index[i];
				}
				else if (task[i] <= 255) {
					user_task[u++] = index[i];
				}
			}
			system_task[s] = -1;
			user_task[u] = -1;
		}
		
		private int[] sort(int task[]) {
			int[] num = new int[task.length];
			for (int i = 0; i < num.length; i++) {
				num[i] = i;
			}
			for (int i = 1; i < task.length; i++) {
				for (int k = 0; k < task.length - i; k++) {
					if (task[k] > task[k + 1]) {
						int temp = task[k];
						task[k] = task[k + 1];
						task[k + 1] = temp;
						
						temp = num[k];
						num[k] = num[k + 1];
						num[k + 1] = temp;
					}
				}
			}
			return num;
		}
	}
```
	

###题7

这应该是一道C语言的题，用java属于偷懒了。题很长，其实就是字符串验证。要用C做，还真的得一种情况一种情况的验，用java可以简单的引用正则表达式，这就无敌了。

```java
/**
	 * 问题描述：
	 * 我国大陆运营商的手机号码标准格式为：国家码+手机号码，
	 * 例如：8613912345678。特点如下：
	 * 1、  长度13位；
	 * 2、  以86的国家码打头；
	 * 3、  手机号码的每一位都是数字。
	 * 请实现手机号码合法性判断的函数（注：考生无需关注手机号码的真实性，
	 * 也就是说诸如86123123456789这样的手机号码，我们也认为是合法的），
	 * 要求：
	 * 1）  如果手机号码合法，返回0；
	 * 2）  如果手机号码长度不合法，返回1
	 * 3）  如果手机号码中包含非数字的字符，返回2；
	 * 4）  如果手机号码不是以86打头的，返回3；
	 * 【注】除成功的情况外，以上其他合法性判断的优先级依次降低。也就是说，
	 * 如果判断出长度不合法，直接返回1即可，不需要再做其他合法性判断。
	 * 要求实现函数：
	 * int s int verifyMsisdn(char* inMsisdn)
	 * 【输入】 char* inMsisdn，表示输入的手机号码字符串。
	 * 【输出】  无
	 * 【返回】  判断的结果，类型为int。
	 * 示例
	 * 输入：  inMsisdn = “869123456789“
	 * 输出：  无
	 * 返回：  1
	 * 输入：  inMsisdn = “8813912345678“
	 * 输出：  无
	 * 返回：  3
	 * 输入：  inMsisdn = “8613912345678“
	 * 输出：  无
	 * 返回：  0
	 * @author liu
	 *
	 */
	public class Test07 {
		public static void main(String[] args) {
			String inMsisdn = "8813912345678";
			System.out.println(new Test07().verifyMsisdn(inMsisdn));
		}
		
		public int verifyMsisdn(String inMsisdn) {
			if (13 != inMsisdn.length()) {
				return 1;
			}
			else if (!inMsisdn.matches("[0-9]*")) {
				return 2;
			}
			else if (!inMsisdn.startsWith("86")) {
				return 3;
			}
			return 0;
		}
	}
```
	

##总结一下

代码在[我的github](https://github.com/liuyuping459/LearningDemo/tree/master/src/com/huawei)仓库中。
总结一下，主要都是对字符、字符串、数组的基本应用的考察。要灵活地应用字符串和数组，会简单的排序，就基本可以解决问题。
还有就是大家一定会发现题目的难度其实是不一样的，有一道基础，一道中级，一道高级三道题组成一套题。

题目来自[yuan22003的专栏](http://blog.csdn.net/yuan22003),表示感谢。
