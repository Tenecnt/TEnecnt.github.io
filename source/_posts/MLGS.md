---
title: 【清华集训2014】玛里苟斯
top: 1
mathjax: true
date: 2018-10-15 20:46:02
tags:
	- 线性基

cover_picture: images/Malygos.jpg
---

[![LaLaLa](images/Malygos.jpg)](http://uoj.ac/problem/36)

线性基模板题

首先是个结论:

所有出现过的数出现的概率是相等的

很容易感性理解

然后,我们考虑$$k==1$$的情况

显然这比较简单,找到所有有1的位并起来直接求除以$$2$$就好了

然后是$$k==2$$

这里枚举任意两位合并的贡献,显然,在所有数的这两位相等并出现过一时答案和$$k==1$$一样
否则就除以$$4$$就好了(容易理解)

然后是$$K>2$$

这里注意到一个性质,答案不大于$$2^64$$

所以线性基个数总共也没多少个,暴力$$2^n$$即可

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

/*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*/
#define MN 200005
#define ulg unsigned lg
int n,k;
ulg a[MN];

void s1(){
	ulg G=0;
	for(int i=1;i<=n;++i)G|=a[i];
	cout<<(G>>1);
	if(G&1)cout<<".5";
}

void s2(){
	ulg res=0;
	for(int i=32;~i;--i){
		for(int j=32,fi,fj,ft;~j;--j){
			fi=fj=ft=0;
			for(int k=1;k<=n;++k){
				fi|=a[k]>>i&1;
				fj|=a[k]>>j&1;
				ft|=(a[k]>>i&1)^(a[k]>>j&1);
			}
			if(!fi||!fj)continue;
			res+=(1ll<<(i+j-ft));
		}
	}
	cout<<(res>>1);
	if(res&1)cout<<".5";
}

ulg p[99],g[99];int tg=0;
void Asuna(){
	for(int i=1;i<=n;++i){
		for(int j=63;~j;--j){
			if(a[i]>>j&1){
				if(!p[j]){p[j]=a[i];break;}
				else a[i]^=p[j];
			}
		}
	}
	for(int i=0;i<=63;++i)if(p[i])g[tg++]=p[i];
	ulg res=0,ans=0;
	for(ulg i=0;i<1<<tg;++i){
		ulg tot=0,G=0,B=1;
		for(int j=0;j<tg;++j)if(i>>j&1)tot^=g[j];
		for(int j=1;j<=k;++j){
			G*=tot;B*=tot;
			G+=B>>tg;B&=(1<<tg)-1;
		}
		res+=G;ans+=B;
		res+=ans>>tg;ans&=(1<<tg)-1;
	}
	cout<<res;
	if(ans)cout<<".5";
}

int main(){
	read(n);read(k);
	for(int i=1;i<=n;++i){
		read(a[i]);
	}
	if(k==1)s1();
	else if(k==2)s2();
	else Asuna();
	return 0;
}

```
