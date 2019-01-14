---
title: 线性规划小记
top: 1
mathjax: true
date: 2019-01-14 19:01:33
tags: 
	-线性规划
---

突然觉得在关键的时候[线性规划](http://uoj.ac/problem/179)还是很重要的

同时突然意识到其实单纯形挺好写的

主要的重点在于pivot

```cpp
void tovip(int x,int idx){
	swap(lder[x],id[idx]);
	db v=1.0/g[x][idx];g[x][idx]=1;
	for(int i=0;i<=n;++i){
		g[x][i]*=v;
	}
	for(int i=0;i<=m;++i)if(i!=x&&abs(g[i][idx])>eps){
		v=g[i][idx];g[i][idx]=0;`
		for(int j=0;j<=n;++j){
			g[i][j]-=g[x][j]*v;
		}
	}
}
```

单纯形的大致思路其实挺好解释的
我们先考虑所有$$b_{i}>0$$的情况
首先,把几个约束解决掉
用$$x_{n+1},x_{n+2}...x_{n+m}$$这几个新加入的变量使不等式约束化为等式

$$
\begin{cases}
   a_{1,1}x_{1}+a_{1,2}x_{2}+...+a_{1,n}x_{n}+x_{n+1}=b_{1}\\
   a_{2,1}x_{2}+a_{2,2}x_{2}+...+a_{2,n}x_{n}+x_{n+2}=b_{2}\\
   ...\\
   a_{m,1}x_{1}+a_{m,2}x_{2}+...+a_{m,n}x_{n}+x_{n+m}=b_{m}\\
\end{cases}
$$

这里我们时刻保证最后的那个变量系数为1

目标函数不变

这时候我们命$$x_{1},x_{2},x_{3},...,x_{n}$$为基变量

我们假设它们都是0

发现这肯定是合法的

函数值为0

但不一定是最优的

怎么办呢

我们有pivot

pivot就是把基变量中的一个换掉

称之：入基离基

这时候，为了这个变换，所有系数和常数项都会改变(因为我们保证最后一个变量的系数为1）

于是，这时的目标函数的常数项不为0了，我们有了更优的答案

但什么时候最优呢？

当然是目标函数全部系数小于0时了

很好理解吧

那么，对于一开始常数项小于0的情况

我们只要不停变换知道一个合法情况就行了，否则就是无解

[uoj97分代码](http://uoj.ac/submission/311549)
```cpp
#include<bits/stdc++.h>
using namespace std;

#define pii pair<int,int>
#define mp make_pair
#define pb push_back
#define lg long long
#define db double
#define lb(x) ((x)&-(x))
#define ft first
#define sd second

#define HII cerr<<"HI"<<endl
#define LLLINE cerr<<"@@@@@@@@@@@@@@@@@@@@"<<endl

template <class _T_>
void read(_T_& d){
	d=0;int f=1;char c=getchar();
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f*=-1;
	for(;c>='0'&&c<='9';c=getchar())d=d*10+c-'0';
	d*=f;
}

/************************************************/
#define MN 250
#define GG return 0
const db eps=1e-8;
namespace Y{
	int n,m;
	int lder[MN];
	int id[MN];
	db g[MN][MN];

	void tovip(int x,int idx){
		swap(lder[x],id[idx]);
		db v=1.0/g[x][idx];g[x][idx]=1;
		for(int i=0;i<=n;++i){
			g[x][i]*=v;
		}
		for(int i=0;i<=m;++i)if(i!=x&&abs(g[i][idx])>eps){
			v=g[i][idx];g[i][idx]=0;
			for(int j=0;j<=n;++j){
				g[i][j]-=g[x][j]*v;
			}
		}
	}

	bool tini(){
		for(int x=0,y=0;;tovip(x,y),x=0,y=0){
			for(int i=1;i<=m;++i)if(g[i][0]<-eps&&(!x||(rand()&1)))x=i;if(!x)break;
			for(int i=1;i<=n;++i)if(g[x][i]<-eps&&(!y||(rand()&1)))y=i;if(!y)GG;
		}return 1;
	}

	int xelpmis(){
		if(!tini())return puts("Infeasible"),1;
		for(int x=0,y=0;;tovip(x,y),x=0,y=0){
		//	cerr<<"    ";for(int i=1;i<=n;++i)cerr<<id[i]<<' ';cerr<<endl;
		//	for(int i=0;i<=m;++i){
		//		cerr<<lder[i]<<' ';
		//		for(int j=0;j<=n;++j){
		//			cerr<<g[i][j]<<' ';
		//		}cerr<<endl;
		//	}
			for(int i=1;i<=n;++i)if(g[0][i]>eps&&(!y||(rand()&1)))y=i;if(!y)break;
			for(int i=1;i<=m;++i){
				if(g[i][y]>eps&&(!x||g[i][y]*g[x][0]>g[i][0]*g[x][y]))x=i;
			}if(!x)return puts("Unbounded"),2;
		}return 0;
	}
}

db res[MN];
int main(){
	int t;
	read(Y::n);read(Y::m);read(t);
	int n=Y::n,m=Y::m;
	for(int i=1;i<=n;++i){
		read(Y::g[0][i]);
	}
	for(int i=1;i<=m;++i)Y::lder[i]=i+n;
	for(int i=1;i<=n;++i)Y::id[i]=i;
	for(int i=1;i<=m;++i){
		for(int j=1;j<=n;++j){
			read(Y::g[i][j]);
		}read(Y::g[i][0]);
	}
	if(!Y::xelpmis()){
		printf("%.8lf\n",-Y::g[0][0]);
		if(t){
			for(int i=1;i<=m;++i){
				res[Y::lder[i]]=Y::g[i][0];
			}
			for(int i=1;i<=n;++i)printf("%.8lf ",res[i]);
		}
	}GG;
}
```
