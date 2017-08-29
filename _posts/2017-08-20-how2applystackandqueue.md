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
<h3 id="formula"> 栈在表达式求值中的应用</h3>
表达式求值是程序设计语言编译中的一个最基本的问题，它的实现是栈应用的一个典型范例。  

下面介绍几个概念（这是我所理解的概念，非严谨概念）：
+ 中缀表达式  
   运算数和操作符依次出现的表达式。中缀表达式不仅需要考虑运算符优先级，还需要考虑括号。如a+b×(c-d)-e/f。   
+ 后缀表达式  
   操作符出现在运算数之后且考虑了优先级的表达式。由于考虑了优先级，所以没有括号。如上式中的中缀表达式可化为abcd-×+ef/-

下面介绍一个简单直观、广为使用的算法，通常称为**“算符优先法”**。  
要把一个表达式翻译成正确求值的一个机器指令序列，或者直接对表达式求值，首先要能够正确解释表达式。例如，要对下面的算术表达式求值：  
<center>4+2×3-10/5</center>  
首先要了解算数四则运算的规则。即：    

1. 先乘除，后加减；
2. 从左往右算；
3. 先括号内，后括号外。

由此，这个算术表达式的计算顺序应为：  
<center>4+2×3-10/5 = 4+6-10/5 = 10-10/5 = 10-2 = 8</center>  
算符优先法就是根据这个运算优先关系的规定来实现对表达式的编译或解释执行的。  

任何一个表达式都是由操作数（operand）、运算符（operator）和界限符（delimiter）组成的，我们称它们为单词。一般的，操作数既可以是常数也可以是被说明为变量或常量的标识符；运算符可以分为算术运算符、关系运算符和逻辑运算符三类；基本界限符有左右括号和表达式结束符等。

这里仅讨论简单算术表达式（只包含加减乘除）的求值问题。  
将运算符和界限符统称为算符，用集合OP来表示。在运算的每一步中，任意两个相继出现的算符θ1、θ2之间的优先关系至多是下面三种关系之一。
* θ1 < θ2 ： θ1的优先权低于θ2
* θ1 = θ2 ： θ1的优先级等于θ2
* θ1 > θ2 ： θ1的优先级高于θ2   

下表展示了这种运算优先级的关系：  
![op](../assets/img/EEImgs/opdiagram.png)  
值得注意的地方时，由于上面的运算规则3，所以表中的“+、-、/、*”运算的优先级均低于左括号，但高于右括号。由运算规则2，当两个相同运算符匹配时，令θ1>θ2。当左右括号相遇时，代表括号内运算已经完成，为了方便，表达式最左侧与最右侧均自动填充一个“#”字符来代表表达式的范围，当两个“#”相遇时，表明整个表达式已经计算完毕。假设输入的表达式不会包含语法错误，所以上表中“)”和“(”，“(”和“#”等无优先级关系。

算法的基本思想如下：  
使用两个工作栈，一个用于寄存运算符（称之为OPTR），一个用于寄存操作数或运算结果（称为OPND）。  
首先置OPND为空栈，表达式起始符“#”为OPTR的栈底元素。  
依次读入表达式中的每个字符，若是操作数则进OPND栈，若是运算符则和OPTR的栈顶运算符比较优先级后进行相应操作，直至整个表达式求值完毕（OPTR的栈顶元素和当前读入的字符均为“#”）。

下面的代码给出了这种思路的基本实现。
```cpp

OPNDType EvaluateExpression()
{
    stack<OPTRType> optr;
    stack<OPNDType> opnd;

    optr.push('#');
    c = getchar();
    while(c != '#' || optr.top() != '#')
    {
        if (!IsOp(c))
        {
            opnd.push(c);
            c = getchar();
        }
        else 
        {
            switch(CmpOp(c,optr.top()))
            {
                case '<':
                    optr.push(c);
                    c = getchar();
                    break;
                case '=':
                    optr.pop();
                    c = getchar();
                    break;
                case '>':
                    OPTRType theta = optr.pop();
                    OPTRType a = opnd.pop();
                    OPTRType b = opnd.pop();
                    opnd.push(GetResult(a, theta, b));
                    break;
            }
        }
    }
    return opnd.top();
} 

//其中  
bool IsOp(char );   //判断输入的参数是否为操作符  
char CmpOp(char ,OPTRType );    //比较运算符优先级，返回值为/</>/=  
OPNDType GetResult(OPNDType, OPTRType, OPNDType);   //返回两个数与一个运算符的计算结果
```
本问题也可以只用一个栈来解决，其基本思路与上文完全一致，不同的地方在于输入的时候需要将表达式转化为后缀表达式后再输入。


---