---
layout: post
title: "基础数据结构之线性表"
image: ''
date:  2017-07-20 14:48:28
tags:
- invisible
description: ''
categories:
- 数据结构
---

--- 
## 线性表的定义与基本操作
### 定义
线性表是具有**相同**数据类型的n(n>=0)个数据元素的**有限**序列。  

###基本操作
主要操作如下：
```md
+ SeqList Init(); - 初始化，构建一个空的线性表
+ int Length(); 求表长
+ SeqData FindByValue(value ); - 按值查找，给定关键字值查找对应元素
+ SeqData FindByPos(pos ); - 按位查找，给定位置查找该位置上的元素
+ void Insert(data , pos); - 插入操作，在pos位置插入元素
+ void Delete(pos); - 删除操作，删除pos位置上的元素
+ void Print(); - 按顺序输出该表内所有元素
+ bool IsEmpty(); - 判空操作
+ void Destroy(); - 销毁线性表并释放空间
```

---
## 线性表的顺序表示
### 定义
线性表的顺序表示又称为顺序表，其特点是**表中元素的逻辑顺序与物理顺序相同**，其典型代表为数组。  
假定顺序表中的元素类型为SeqData，则线性表的存储类型描述为：  
```cpp
#define MAXSIZE 1024
typedef struct SeqList
{
  SeqData* data;
  int maxSize;
  int length;
};
```
### 动态分配空间与静态分配空间
线性表的空间可以是动态分配的，也可以是静态分配的。在程序中，一般采用动态分配的方式来申请空间，防止线性表内存溢出导致程序崩溃。  
动态分配的语句为：
```cpp
SeqList seqLst;
//cpp
seqLst.data = new SeqData[MAXSIZE];
//c
seqLst.data = (SeqData*)malloc(sizeof(SeqData) * MAXSIZE);
```
静态分配语句即为数组分配语句：
```cpp
#define MAXSIZE 1024
typedef struct SeqList
{
  SeqData data[MAXSIZE];
  int length;
};

SeqList seqLst;
```
顺序表最主要的特点是随机访问，只要知道了首地址和元素序号即可在O(1)的时间内找到该元素。  

### 顺序表基本操作的实现
由于其他操作非常简单，这里只实现插入(Insert)，删除(Delete)和按值查找(FindByValue)。
```cpp
typedef struct SeqData
{
	int value;
};

class SeqList
{
	private:
		int length;
	public:
		SeqData* data;
		int maxLen;

		SeqList(int maxLen)
		{
			data = new SeqData[maxLen];
			this->maxLen = maxLen;
			length = 0;
		}
		void Insert(int pos, SeqData e)
		{
			if (pos<1 || pos>maxLen)
			   return ;
			if (GetLength() >= maxLen)
			   return ;

			for (int i=GetLength(); i>=pos; i--)
			{
				data[i]=data[i-1];
			}
			data[pos-1]=e;
			length++;
		}
		void Delete(int pos)
		{
			if (pos<1 || pos>max)
			   return ;

			for (int i=pos; i<GetLength(); i++)
				data[i-1]=data[i];

			length--;
		}
		SeqData FindByValue(int v)
		{
			int pos = 0;
			for (; pos<GetLength(); pos++)
				if (data[pos].value == v)
				   return data[pos];

	        return NULL;
		}
		int GetLength()
		{
			return length;
		}
}

```

---
## 线性表的链性表示
