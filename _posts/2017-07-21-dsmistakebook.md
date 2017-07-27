---
layout: post
title: "数据结构错题本"
image: ''
date:  2017-07-21 15:21:16
tags:
- invisible
description: ''
categories:
- 数据结构
---

---
## 线性表

**[1]**. 若线性表最常用的操作方式是存取第i个元素及其前驱元素和后继元素的值，为了提高效率，应该采用（ ）的存储方式？  
A. 单链表
B. 双向链表
C. 单循环链表
D. 顺序表

**分析：**  
本题正确答案是D，错选了B。题目明显是需要存储i,i+1,i-1位置元素的值，顺序表可以直接通过下标随机访问到这三个值。时间复杂度为O(1),
而其他所有选项都需要遍历来获取第i个元素的值。时间复杂度为O(n)。  

**[2]**. 实现从有序顺序表中删除其值在给定s和t(s<t)之间的所有元素，如果s,t不合理则显示错误信息并退出运行的算法。

**分析：**  
这题我没有理解清楚题目意思，  
1. 题目给定的是有序表，但是没有说明是升序还是降序。这里应该从两个方面讨论，但是参考答案只给出了升序表的
处理方案，于是认为有序表指的是升序表。
2. 题目给出的s,t区间未说明是开区间(s,t)还是闭区间[s,t]，我理解为闭区间，参考答案上是闭区间[s,t]。

我自己的答案：
```cpp
void DeletRange(SeqData& seq, SeqData min, SeqData max)
{
	if (min >= max)
	{
		puts("Error Info: Min-value > Max-value!");
		//exit(-1);
		return ;
	}

	if (seq.IsEmpty())
	{
		puts("Error Info: Sequence list is empty!");
		//exit(-1);
		return ;
	}

	int len = seq.GetLength();
	int start = -1;
	for (;start<len;start++)
		if (seq.data[start++] > min)
		   break;

    if (start < 0)
    {
    	puts("Error Info: Min-value is too large!");
    	//exit(-1);
		return ;
	}

	int end = start;
	for (; end<len; end++)
		if (seq.data[end] > max)
		   break;

    if (end == len)
    {
		seq.Clear();
		return ;
	}

	for (; end<len; )
		seq.data[start++] = seq.data[end++];

	seq.length -= (max-min-1);
}
```

参考答案如下：
```cpp
bool DeleteRange(SeqList& seq, SeqData min, SeqData max)
{
	int i,j;
	if (min >= max || seq.length == 0)
	   return false;

  //注意这里的for循环内没有执行任何语句
  for (i=0; i<seq.length && seq.data[i]<min; i++) ;

	if (i >= seq.length)
	   return false;

  //注意这里的for循环内没有执行任何语句
  for (j=i; j<seq.length && values[j]<=max; j++) ;

  for (; j<seq.length; i++,j++)
    values[i] = values[j];

  seq.length = i;
  return true;
}
```

参考答案通过bool值来返回操作是否成功，但是并未输出错误信息。个人认为不严谨。两个算法的思路是一样的，时间复杂度也一样，同为O(n)。


【2013年计算机联考题】  
**[3]**. 已知一个整数序列A= (a0,a1,a2,...,a(n-1))，其中0≤ai<n (0≤i<n)。若存在a(p1)=a(p2)=a(p3)=...=a(pm)=x
且m>n/2(0≤pk<n, 1≤k≤m)，则称x为A的主元素。例如A=(0,5,5,3,5,7,5,5)，则5为主元素；又如A=(0,5,5,3,5,1,5,7)，则A中没有主元素。
假设A中的n个元素保存在一个一维数组中，请设计出一个尽可能高效的算法，找出A中的主元素。若存在主元素，则输出该元素；否则输出-1。
要求：  
(1) 给出算法的基本设计思想。  
(2) 根据设计思想，采用C/C++/Java语言描述算法，关键之处给出注释。  
(3) 说明该算法的时间复杂度和空间复杂度。  

我的答案：  
(1) 先对A中所有元素进行排序，然后统计排序后出现次数最多的元素，如果该元素出现次数>m/2则为主元素，否则没有主元素。  
(2)
```cpp
int GetMainElement(int* a, int len)
{
	//先对数组进行排序
	Sort(a,len);

	//mainCount记录主元素出现的次数，currentValue记录当前统计的元素值
	int mainCount=1, currentValue=a[0];

	for (int i=1; i<len; i++)
	{
	    if (a[i] == currentValue)
				mainCount++;
	    else
	    {
	    	currentValue = a[i];	//更换统计的元素值
				mainCount = 1;	//重置主元素个数
			}
	}

	//对主元素出现个数进行判断，大于长度为
	return mainCount > len/2 ? currentValue : -1;
}

void Sort(int *a, int len)
{
	for (int i=0; i<len; i++)
		for (int j=0; j<len-i-1; j++)
			if (a[j] > a[j+1])
			   a[j]^=a[j+1]^=a[j]^=a[j+1]; 	//使用位运算无需中间变量即可更换元素值
}
```
(3) 该算法的时间复杂度为O(n^2)，空间复杂度为O(1)。

**分析：**   
本体满分15分，以上解法只能拿到10分。主要丢分点在于时间复杂度太高，该题最优时间复杂度为O(n)，即使是采用快排也可以将时间复杂度缩小到
O(nlog(2)n) （可以拿到11分），但是不知道考试中是否可以直接调用C++库函数sort。  
因此在平时的积累中，应该对排序算法多加深印象，但是这个分值设定也提醒我，**花费大量时间去思考最优算法是得不偿失的** 。  

参考答案：  
(1):   
算法策略是从前向后扫描数组个数，标记出一个可能成为主元素的元素Num，然后重新计数，确认Num是否为主元素。  
算法可以分为以下两步：  
① 选取候选的元素：依次扫描所给数组中的个整数，将第一个遇到的整数num保存到c中，记录Num的出现次数为1，若遇到的下一个整数仍为num，则计数+1，
否则计数减1，当计数减到0时，将遇到的下一个整数保存到c中，计数重新记为1，开始新一轮计数，即从当前位置开始重复上述过程，直到扫描完全部数组元素。  
② 判断c中元素是否为真正的主元素，再次扫描该数组，统计c中元素出现的个数，若大于n/2则为主元素，否则不存在主元素。  

(2):  
```c
int Majority(int A[], int n)
{
	int i,c,count=1;							//c用来保存候选主元素，count用来计数
	c=A[0];												//设置A[0]为主元素
	for (i=1; i<n; i++)						//查找候选主元素
		if (A[i] == c)
			count++;									//对A中的候选主元素计数
		else
			if (count > 0)						//处理不是候选主元素的情况
				count--;
			else											//更换候选主元素，重新计数
			{
				c = A[i];
				count = 1;
			}
	if (count > 0)
		for (i=count=0; i<n; i++)		//统计候选主元素出现的实际个数
			if (A[i] == c)
				count++;

	if (count > n/2)							//确认候选主元素
		return c;
	else													//不存在候选主元素
		return -1;
}  
```
(3) 算法的时间复杂度为O(n)，空间复杂度为O(1)








<center>  
 <a href="../entranceExamSummary">返回目录</a>
</center>
