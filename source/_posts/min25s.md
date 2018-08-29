---
title: Min_25筛
top: 1
mathjax: true
date: 2018-07-27 20:35:50
tags:
	- 数论
	- 数论筛
---
这个东西续了我好长时间，并且让我意识到状态真的不太对

Min_25筛这个东西，感觉对于不同题，思路上还是有些不同的，只是大致方向一致，所以也没有很多具体模板这种东西吧

###大致思路是这样的：

首先，处理的内容大致就是积性函数~~或者什么类似积性函数的奇怪东西~~前缀和，复杂度大概是什么$$O(n^{0.69})$$(这个的证明真的不会)

我们假设如此一个问题：

$$
f(n) = \begin{cases}
1& if\;n = 1\\
g(p,e)& if\;n = y^e,\;e > 0,\;p\;is\;a\;prime \\
f(x)*f(y) & if\;x*y=n,\;x\perp y \\
\end{cases}
$$

然后求$$S(N)=\displaystyle\sum_{1 \leqslant j \leqslant N} f(j)$$

考虑到一个数除以其最大质因数后一定不剩大于$$\sqrt{N}$$的质因数，这是我们解决这个问题的关键。

考虑枚举因数$$M$$:
$$\begin{aligned}
& M' = M/P^{e}\;,e>0\\
& P\;is\;the\;max\;prime\;factor\;of\;M
\end{aligned}$$

根据这是个积性函数，可知：
$$
\begin{aligned}& f(N)=\sum_{M'} f(M')*(1+\sum_{F < p \leqslant N/M'} f(p))\\& F\;is\;the\;largrst\;prime\;factor\;of\;M'\;and\;p\;is\;a\;prime
\end{aligned}$$

(这只是个大致的式子，不完全对，细节可能有问题，可能需要调整)

那么$$f(M')$$可以递归解决，问题在于求$$\sum_{F < p \leqslant N/M'} f(p)$$(自此以下$$p$$都为质数)

首先，设$$h(x)=\sum_{p \leqslant x} f(p)$$，将其转化为$$h(N/M')-h(F-1)$$

那么解决了$$h(x)$$的求解就能解决问题了

对此如何处理呢？

首先，如果$$p$$不止是质数的话(将合数看做质数处理)，大部分的前缀和都能$$O(1)$$求出

例如$$\sigma(n^k)$$(n的k次的因子数)

然后考虑筛的实现

我们回想有什么筛是大家熟知的

埃拉托色尼筛？

考虑像埃拉托色尼筛一样搞

埃拉托色尼筛怎么搞的？

每个数$$p$$用来筛大于$$p^2$$的所有数

所以这里也是这样：从小到大枚举每个质数，用其对大于其平方的数的前缀和进行处理

(这里注意，要处理$$h(x)$$的数仅有$$O(\sqrt{N})$$个，为$$1,2,3,...,N/3,N/2,N/1$$)

这样就能完美筛出$$h(x)$$了

有这么几道具体题目：

---

### [spoj-DIVCNTK](https://www.spoj.com/problems/DIVCNTK/)

这题就是求$$f(n)=\displaystyle\sum_{1 \leqslant i \leqslant n}\sigma(i^k)$$

这里首先去掉1这个因数，就成了积性函数。

而$$f(p)=k$$的前缀和可以轻松求出。

直接上就好

```cpp
#include<bits/stdc++.h>
using namespace std;

#define MN 4000005
#define lg unsigned long long

int pri[MN],totp=0,S;
bool vd[MN];
lg ns[MN],tns=0,h[MN];

lg n,k;

int D(lg X){
	return X<=S?X:tns-n/X+1;
}

lg f(lg n,int g){//递归求解f,f(n,g)☞n以内不包括大于pri[g]的质因数的数
	if(n<1||pri[g+1]>n)return 0;
	lg res=h[D(n)]-g*(k+1);
	while(1ll*pri[g+1]*pri[g+1]<=n){
		int p=pri[g+1];
		lg t=n/p,e=k+1;
		while(t>=p)res+=f(t,g+1)*e+e+k,e+=k,t/=p;
		++g;
	}return res;
}

lg slv(lg n){
	S=sqrt(n);//处理h(x)
	tns=0;
	for(int i=1;i<=S;++i){
		ns[++tns]=i;
	}
	for(int i=S;i;--i){
		if(n/i>S)ns[++tns]=n/i;
	}
	for(int i=1;i<=tns;++i){
		h[i]=(ns[i]-1)*(k+1);
	}
	int H=1;lg up=0;
	for(int P=1;pri[P]<=S;++P){
		int p=pri[P];
		while(ns[H]<1ll*p*p)++H;
		for(int i=tns;i>=H;--i){
			h[i]-=h[D(ns[i]/p)]-up;
		}up+=k+1;
	}
	return f(n,0)+1;
}

void init(){
	for(int i=2;i<MN;++i){
		if(vd[i])continue;
		pri[++totp]=i;
		for(int j=i+i;j<MN;j+=i)vd[j]=1;
	}
}

int main(){
	init();int Cas;
	for(scanf("%d",&Cas);Cas;--Cas){
		scanf("%llu%llu",&n,&k);
		printf("%llu\n",slv(n));
	}
	return 0;
}
```

### [APS2](https://www.spoj.com/problems/APS2/)

这题求的是最小质因数前缀和

考虑枚举最小质因数，要乘的就是最小质因数大于等于与$$n$$的数的个数

这个就可以用Min_25筛搞了

```cpp
#include<bits/stdc++.h>
using namespace std;

#define MN 4000005
#define ulg unsigned long long

int pri[MN],totp=0,S;
ulg tp[MN];
bool vd[MN];
ulg ns[MN],tns=0,h[MN],d[MN];

ulg n;

int D(ulg X){
	return X<=S?X:tns-n/X+1;
}

ulg c(ulg n,int g){
	if(n<1||pri[g]>n){
		return 0;
	}
	ulg res=d[D(n)]-d[D(pri[g]-1)];
	while(1ll*pri[g]*pri[g]<=n){
		ulg t=n/pri[g];
		while(t>=pri[g]){
			res+=c(t,g+1);
			++res;
			t/=pri[g];
		}++g;
	}
	return res;
}

ulg f(ulg n,int g){
	if(n<1||pri[g]>n)return 0;
	ulg res=h[D(n)];
	while(1ll*pri[g]*pri[g]<=n){
		ulg t=n/pri[g];
		res+=c(t,g)*pri[g];
		++g;
	}return res;
}

ulg slv(ulg n){
	S=sqrt(n);tns=0;
	for(int i=1;i<=S;++i){
		ns[++tns]=i;
	}
	for(int i=S;i;--i){
		if(n/i>S)ns[++tns]=n/i;
	}
	for(int i=1;i<=tns;++i){
		if(!(ns[i]&1))h[i]=(ns[i]+2)/2*(ns[i]-1);
		else h[i]=(ns[i]-1)/2*(ns[i]+2);
		d[i]=ns[i]-1;
	}
	int H=1;
	for(int p=2;p<=S;++p)if(!vd[p]){
		while(ns[H]<1ll*p*p)++H;
		for(int i=tns;i>=H;--i){
			h[i]-=(h[D(ns[i]/p)]-h[p-1])*p;
			d[i]-=(d[D(ns[i]/p)]-d[p-1]);
		}
	}
	return f(n,1);
}

void init(){
	for(int i=2;i<MN;++i){
		if(vd[i])continue;
		pri[++totp]=i;
		for(int j=i+i;j<MN;j+=i)vd[j]=1;
	}
	for(int i=2;i<MN;++i)tp[i]=pri[i]+tp[i-1];
}

int main(){
	init();int Cas;
	for(scanf("%d",&Cas);Cas;--Cas){
		scanf("%lld",&n);
		printf("%llu\n",slv(n));
	}
	return 0;
}
```
