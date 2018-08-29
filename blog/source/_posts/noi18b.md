---
title: NOI2018冒泡排序(inverse)
date: 2018-07-19 20:22:44
tags:
	- NOI
	- 组合数学
top: 1

mathjax: true
---

[这道题](https://judge-duck.online:10086/problem/noi18b)的冒泡排序好像是假的

首先，根据那个啥的定义，首先必须得到坏序列的充要条件是 

$$\exists\;{i<j<k},a_{i} > a_{j} > a_{k}$$

证明就感性理解一下，对于中间那个数，无论它目标位置是在左边还是右边，都有至少一步向反方向移动，所以一定是坏序列了

###80
---
然后80分就很简单了，设计这么一个函数，$$F_{i,j}$$表示目前有i个数需要排列，但是由于前面的数的限制，最小的$j$个数的相对顺序必须递增。

容易得到递推式： 

$$ F_{i,j} = \sum^{j-1}_{k=i} F_{i-1,k}$$

其实就是枚举拿的是哪个数，最小数就是$$F_{i-1,k}$$,否则对应了各个数。

然后就拿这个东西像数位dp一样扫一遍过去就是O($$n^2$$)了

###100
---
满分算法就比较神仙了

曾经有这样一道[题](https://www.codechef.com/APRIL18B/problems/VAIMIN)

为了解决在一条直线限制下A点走到B点的方案数，这里用到了一种高妙的方法，将起点按对称线翻转计数，这里的每一种方案刚好一一对应了一种原起点到终点的一种路线(想象将该路线第一次遇到对称轴前的路径翻折)，于是这样，就能解决上述的小问题。

那么，对于这个问题，我们可以发现，在dp转移的时候，其实对应了一个长度为2n的括号序列。

每次转移对应了在原串后面加上了若干左括号和一个右括号（当取的是最小数时对应0个左括号+1个右括号）

然后将左括号和右括号转化成在二维坐标上的两个向右上（下）45度的向量，所以这就成了一个在二维上自由行走（但不能低于x轴）的问题，同时必须满足中途没有低于过一条线（除非已经高于过这条直线）。

于是想80分一样，对于每一位统计在这位超过限制的答案数，这里可以发现，只要先多出现了一个左括号，后面的左括号都能在自由行走中补充，所以每位的统计可以O(1)，这就使总体复杂度化为O(n)了


~~可是我为了偷懒，没有维护目前比最大值小的值数，用树状数组，多了一个log~~

~~能过就行~~


```cpp
#include<algorithm>
#include<cstdio>
#include<cstring>
using namespace std;

#define lg long long
#define MN 1200005
#define lsk 998244353
#define lb(x) ((x)&(-(x)))

int n;
int a[MN>>1],c[MN>>1],D[MN>>1];

int read(){
	static int d;static char c;
	for(d=0,c=getchar();c<'0'||c>'9';c=getchar());
	for(;c>='0'&&c<='9';d=(d<<3)+(d<<1)+c-'0',c=getchar());
	return d;
}

void add(int x,int y){
	for(;x<=n;x+=lb(x))c[x]+=y;
}

int qry(int x){
	int res=0;
	for(;x;x-=lb(x))res+=c[x];
	return res;
}

lg fac[MN],ifac[MN];

lg Pow(lg A,lg B){
	lg res=1;
	for(;B;B>>=1,A=A*A%lsk)if(B&1)res=res*A%lsk;
	return res;
}

void init(){
	fac[0]=ifac[0]=1;
	for(int i=1;i<MN;++i){
		fac[i]=fac[i-1]*i%lsk;
		ifac[i]=Pow(fac[i],lsk-2);
	}
}

lg C(int A,int B){
	return fac[A]*ifac[B]%lsk*ifac[A-B]%lsk;
}

lg Q(int A,int B){
	return C(A,(A+B)/2);
}

lg T(int A,int B){
	if(B<0)return 0;
	return ((Q(A,B)-Q(A,B+2))%lsk+lsk)%lsk;
}

int main(){
	int TT;init();
	scanf("%d",&TT);
	while(TT--){
		memset(D,0,sizeof D);
		memset(c,0,sizeof c);
		scanf("%d",&n);lg res=0;
		for(int i=1;i<=n;++i)add(i,1);
		for(int i=1;i<=n;++i)a[i]=read();
		int mx=0,mn=1;
		for(int i=1;i<n;++i){
			int g=max(mx,a[i]);
			int f=qry(g)+1;
			int d=n*2-(i-1)*2-f;
			res=(res+T(d,f))%lsk;
			D[a[i]]=1;add(a[i],-1);
			if(a[i]>mx)mx=a[i];
			else{
			    if(a[i]!=mn)break;
			}
			while(D[mn])++mn;
		}
		printf("%lld\n",res);
	}
	return 0;
}
```
