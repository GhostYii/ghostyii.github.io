---
layout: post
title: "栈和队列的应用"
image: ''
date:  2017-08-20 15:49:20
tags:
- invisible
description: ''
categories:
- 数据结构
---

--- 
> 快速导航  
> [1. 栈在括号匹配中的应用](#brackets)  
> [2. 栈在表达式求值中应用](#formula)  
> [3. 栈在递归中的应用](#recursive)  
> [4. 队列在层次遍历中的应用](#layer)  
> [5. 队列在计算机系统中的应用](#os)

--- 
<h3 id="brackets"> 栈在括号匹配中的应用</h3>
假设表达式中允许包含两种括号：圆括号和方括号，其嵌套的顺序任意，即( [ ] ( ))或[([][])]等均为正确的格式，[(])或([()或者(()]均为不正确的格式。  
考虑以下括号序列:

![brackets](../assets/img/EEImgs/brackets.png)

分析如下：
1. 计算机接收到第1个括号“[”后，期待与之匹配的第8个括号“]”出现。  
2. 获得了第2个括号“(”，此时第1个括号“[”暂时放在一边，而急迫期待与之匹配的第7个括号“)”的出现。  
3. 获得了第3个括号“[”，此时第2个括号“(”暂且放在一边，而急迫期待与之匹配的第4个括号“]”的出现。第3个括号的期待得到满足，消解之后，第2个括号的期待匹配又成为当前最急迫的任务了。  
4. 依次类推，可见该过程的处理与**栈**的思想吻合。  

算法的思想如下：  
1. 初始设置一个空栈，顺序读入括号。  
2. 若是右括号，则或者使置于栈顶的最急迫期待得以消解，或者是不合法的情况（括号序列不匹配，退出程序）。  
3. 若是左括号，则作为一个新的更急迫的期待压入栈中，自然使原有的在栈中的所有未消解的期待的急迫性降了一级。算法结束时，栈为空，否则括号序列不匹配。  

下面通过一道题目来对这种情况进行详细说明：  

题：假设一个算术表达式中包含圆括号"()"、方括号"[]"和花括号"{}"3种类型的括号，编写一个算法来判别表达式中的括号是否配对，以字符“\0”作为算术表达式的结束符。  

分析：与上文所提到的一致，本题采用栈的思路来解决。  
基本思想是扫描每个字符，遇到左括号进栈，遇到右括号时检查栈顶元素是否是相对应的左括号，若是则弹栈，否则匹配错误。最后的栈如果不为空也为匹配错误。
下面给出一个可运行的代码，为了强调本题思路，所使用的栈非手动实现而使用了C++中STL的stack数据结构。  
```cpp
//一个可运行的非常容易理解的版本
#include <iostream>
#include <stack>
#include <cstring>
using namespace std;

int main()
{
    char str[1024];
    stack<char> s;
    cin>>str;

    for(int i=0; i<strlen(str); i++)
    {
        if (str[i]=='(' || str[i]=='[' || str[i]== '{')
            s.push(str[i]);
        else if (str[i]==')' || str[i]==']' || str[i]== '}')  
        {
            if (s.empty())
            {
                cout<<"error"<<endl;
                exit(0); 
            }
            switch(str[i])
            {
                case ')':
                    if (s.top() == '(')
                        s.pop();
                    else
                    {
                        cout<<"error"<<endl;
                        exit(0); 
                    }
                    break;
                case ']':
                    if (s.top() == '[')
                        s.pop();
                    else
                    {
                        cout<<"error"<<endl;
                        exit(0); 
                    }
                    break;
                case '}':
                    if (s.top() == '{')
                        s.pop();
                    else
                    {
                        cout<<"error"<<endl;
                        exit(0); 
                    }
                    break;
                default: break;
            }		
        } 
    }
    if (!s.empty())
    cout<<"error"<<endl;
    else
    cout<<"success"<<endl;  

    return 0;
} 

//一个更加精简的版本，仅提供方法
bool BracketsCheck(char *str)
{
    stack<char> s;
    int i=0;
    
    while(str[i] != '\0')
    {
        switch(str[i])
        {
            case '(': s.push(str[i]); break;
            case '[': s.push(str[i]); break;
            case '{': s.push(str[i]); break;
            
            case ')': 
                char tmp = s.pop();
                if (tmp != '(')
                    return false;
                break;
            case ']': 
                char tmp = s.pop();
                if (tmp != '[')
                    return false;
                break;
            case '}': 
                char tmp = s.pop();
                if (tmp != '{')
                    return false;
                break;
        }
        i++;
    }

    return s.empty();
}
```


---