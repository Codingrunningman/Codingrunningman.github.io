---
layout: post
title: 函数忘记写返回值会怎样？
date: 2019-03-12 23:00:00
---

返回类型为bool的函数在没有写返回值的情况下会怎么样？先来看一个例子.

事情是这样的, 今天在写代码时出现了一个令人诧异的情况: **printf** 会改变程序的运行结果.

部分代码如下:
```
void DFS(int x,int y,int t)
{
	if(x == ed.x && y == ed.y && t <= td) success = true ;

	for(int i = 0;i < 4;i ++)
	{
		int dx = x + dir[i][0];
		int dy = y + dir[i][1];
		
		// printf("");
		
		if(!check(dx,dy)) continue ;
		if(mark[dx][dy] == true) continue ;
		mark[dx][dy] = true;
		DFS(dx,dy,t+1);
		mark[dx][dy] = false;
		if(success) return ;
	}
	return ;
}
```
当注释掉 **printf** 时，程序会输出错误结果；而去除注释时，程序结果输出正常.
为什么**printf**竟然会影响到程序的运行结果？检查代码后发现，在check()函数中，忘记了一条返回语句
```
bool check(int dx,int dy)
{
	if(dx < 1 || dx > n || dy < 1 || dy > m || maze[dx][dy] == 'X') return false;
	return true;   // 忘记的返回语句
}
```
那么为什么**printf**会对返回值产生影响呢？来查看一下汇编代码:
```
31:           printf("");
0040111C   push        offset string "" (0042501c)
00401121   call        printf (00401430)
00401126   add         esp,4
32:           if(!check(dx,dy)) continue ;
00401129   mov         edx,dword ptr [dy]
0040112C   push        edx
0040112D   mov         eax,dword ptr [dx]
00401130   push        eax
00401131   call        @ILT+0(check) (00401005)
00401136   add         esp,8
00401139   and         eax,0FFh
0040113E   test        eax,eax
00401140   jne         DFS+0A4h (00401144)
00401142   jmp         DFS+49h (004010e9)
```
[注]EAX属于通用寄存器，是很多加法和乘法指令的缺省寄存器. AX寄存器是算术运算的主要寄存器，
所有的输入和输出只使用AX和AL作为数据寄存器.  
|===============EAX===============|--32个0,4个字节，2个字，1个双字  
|======AX========|--16个0,2个字节，1个字  
|=======|===AH===|----8个0,1个字节  
|===AL==|---8个0,1个字节  
由上图可以看出AX和EAX并不独立，改变AX后EAX的值也会发生变化.

我猜测可能由于**printf**将函数返回结果压入EAX寄存器后从而导致了check()在if条件失败时取出了EAX中的值，
形成类似于**return true**的效果，但**printf("")**的返回值为0，这一猜测显然不成立.
同时在visual c++和cfree两种编译环境下，运行结果竟然不同.

该问题暂时不能解决，但可以猜测它与**printf**函数的运行机制有关，我模拟printf写了一个函数，但并未产生同样的效果.
虽然如此，我们还是可以通过这个问题获得一些知识和教训:  
1. 不要忘记给有类型的函数一个返回值，否则会产生出人意料的结果.
2. 不要想当然
3. 事实就是事实，不要试图去强行解释.



