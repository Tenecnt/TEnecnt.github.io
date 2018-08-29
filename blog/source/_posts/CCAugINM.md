---
title: (CC 2018AugestLong)Interactive Matrix
top: 1
mathjax: true
date: 2018-08-09 10:51:29
tags:
	- 交互题
	- 乱搞
---
[这](https://www.codechef.com/AUG18A/problems/INMAT)真是一道神奇的乱搞题

首先限制说明，询问次数可以达到$$4n$$
我们发现，每次询问四个顶点，当一边的两个值都大于$$V$$时，可以除去这一边
如果这样走完整个矩阵，答案刚好$$4n$$
但是这里就出现了所不能解决的情况


| | | |  |
|--|--|--|--|
|大| | |小|
|小| | |大|

这样什么都删不掉

这可如何是好？

注意到上下左右关于$$V$$的大小关系

其一定是


| | | |  |
|--|--|--|--|
|大|大 | 小|小|
|大| 大|大 |小|
|小| 小| 大|大|

或

| | | |  |
|--|--|--|--|
|大|大 | 小|小|
|大| 大| 小|小|
|小|小| 小|大|

这类的情况

也就是说，“大”与“小”分别占据角上一部分，并且扩展方向是单调的

这时爆扫边界，复杂度就是两条边界的长度，但确定边界开始位置时，要爆扫一边，所以最坏情况下要$$6n$$

我码完后才发现这有点假了~~其实本来发现不了的，但交题时网炸了，菊花转不出，这时复杂度一分析好像不对~~，但顺手还是交了一发（毕竟写完了嘛），然后。。。

# 过了！
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MN 1005
int V;
int a[MN][MN];
int b[MN],c[MN];
int k=0,K;

int Qry(int x,int y){
	if(a[x][y])return a[x][y];
	++k;
	printf("1 %d %d\n",x,y);
	fflush(stdout);
	int res;
	scanf("%d",&res);
	if(res==V){
		printf("2 %d %d\n",x,y);
		fflush(stdout);
		exit(0);
	}
	if(k==K){
		puts("2 -1 -1");
		fflush(stdout);
		exit(0);
	}
	return a[x][y]=res;
}

void Rin(int u,int d,int l,int r){
	int Vul=Qry(u,l);
	int Vdl=Qry(d,l);
	int Vur=Qry(u,r);
	int Vdr=Qry(d,r);
	if(Vul>V){
		int x=u,y=l;
		while(x>=u&&x<=d&&y>=l&&y<=r){
			while(y>=l&&y<=r&&Qry(x,y)>V)++y;if(y>r)--y;
			while(y>=l&&y<=r&&Qry(x,y)<V)--y;
			++x;
		}
		x=d,y=r;
		while(x>=u&&x<=d&&y>=l&&y<=r){
			while(y>=l&&y<=r&&Qry(x,y)>V)--y;if(y<l)++y;
			while(y>=l&&y<=r&&Qry(x,y)<V)++y;
			--x;
		}
	}else{
		int x=d,y=l;
		while(x>=u&&x<=d&&y>=l&&y<=r){
			while(y>=l&&y<=r&&Qry(x,y)>V)++y;if(y>r)--y;
			while(y>=l&&y<=r&&Qry(x,y)<V)--y;
			--x;
		}
		x=u,y=r;
		while(x>=u&&x<=d&&y>=l&&y<=r){
			while(y>=l&&y<=r&&Qry(x,y)>V)--y;if(y<l)++y;
			while(y>=l&&y<=r&&Qry(x,y)<V)++y;
			++x;
		}
	}
}

int main(){
	int n;
	scanf("%d%d%d",&n,&K,&V);
	int u=1,l=1,r=n,d=n;
	while(u<=d&&r>=l){
		//cout<<"ID"<<' '<<u<<' '<<d<<' '<<l<<' '<<r<<endl;
		if(Qry(u,l)>V&&Qry(u,r)>V||Qry(u,l)<V&&Qry(u,r)<V){++u;continue;}
		if(Qry(d,l)>V&&Qry(d,r)>V||Qry(d,l)<V&&Qry(d,r)<V){--d;continue;}
		if(Qry(u,l)>V&&Qry(d,l)>V||Qry(u,l)<V&&Qry(d,l)<V){++l;continue;}
		if(Qry(u,r)>V&&Qry(d,r)>V||Qry(u,r)<V&&Qry(d,r)<V){--r;continue;}
		break;
	}
	//cout<<"#####"<<endl;
	if(u>d||r<l){
		puts("2 -1 -1");
		fflush(stdout);
		return 0;
	}
	Rin(u,d,l,r);
	puts("2 -1 -1");
	fflush(stdout);
	return 0;
}
```
