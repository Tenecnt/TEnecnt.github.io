---
title: NOI2018屠龙勇士(dragon)
top: 1
mathjax: true
date: 2018-07-22 19:51:21
tags:
	- 数论
	- CRT
---
好吧[这道题](https://judge-duck.online:10086/problem/noi18d)其实是道CRT裸题……

首先哪条龙选哪把剑是固定的。拿个set(multiset)/map扫一遍即可。~~当然也可以自己写平衡树啊2333~~

然后就是若干 $$ k_{i}x \equiv a_i (mod\;p_i) $$

可以讲整个柿子中的$$ k_i,a_i,p_i $$同除$$gcd(k_i,p_i)$$(如果$$a_i$$无法整除就无解了)，这肯定对答案是无影响的。
然后，江柿子两边同除$$k_i$$(已经有逆元了)，变为$$n$$个形为$$x \equiv a_i/k_i(mod\;p_i)$$的柿子
这就成了裸的CRT了。
对于一个CRT，

$$ \begin{equation}
\begin{cases}
x \equiv a_1(mod\;p_1) \\
x \equiv a_2(mod\;p_2) \\
gcd(p_1,p_2) = d
\end{cases}
\end{equation} $$

可得 

$$x \equiv p_1*(a_2-a_1)*(p_1/d)^{-1}/d+a_1(mod\;n_1n_2/d) $$

于是扫一遍就能A了。
注意爆long long，要写慢速乘
```cpp
#include<bits/stdc++.h>
using namespace std;

#define lg long long
#define MN 200005
#define ft first
#define sd second

lg a[MN],p[MN],k[MN],ne[MN];

int n,m;lg Ep1,Ep2,G,Q;

map<lg,int> M;
map<lg,int>::iterator it;

lg gcd(lg A,lg B){
	return B==0?A:gcd(B,A%B);
}

void exgcd(lg x,lg y,lg& a,lg& b){
	if(y==0)a=1,b=0;
	else exgcd(y,x%y,b,a),b-=x/y*a;
}
lg inv(lg x,lg y){
	lg A,B;
	exgcd(x,y,A,B);
	return (A%y+y)%y;
}

lg Qx(lg x,lg y,lg md){
	lg res=0,f=1;
	if(x<0)x=-x,f=-f;
	if(y<0)y=-y,f=-f;
	for(;y;y>>=1,x=(x+x)%md)if(y&1)res=(res+x)%md;
	res*=f;res%=md;res+=md;res%=md;
	return res;
}

lg work(){
	lg F=1;
	for(int i=1;i<=n;++i){
		int H=gcd(k[i],p[i]);
		if(a[i]%H)return -1;
		k[i]/=H,p[i]/=H,a[i]/=H;
	}
	//for(int i=1;i<=n;++i)cout<<k[i]<<endl;
	for(int i=1;i<=n;++i){
		F=max(F,(a[i]-1)/k[i]+1);
	}
	for(int i=1;i<=n;++i){
		a[i]-=F*k[i];
		a[i]%=p[i];
		a[i]+=p[i];
		a[i]%=p[i];
	}
	if(G==1){
		return F;
	}
	for(int i=1;i<=n;++i){
		lg H=gcd(k[i],p[i]);
		a[i]=Qx(a[i],inv(k[i],p[i]),p[i]);
	}
	lg np=p[1],na=a[1];
	for(int i=2;i<=n;++i){
		lg E=gcd(np,p[i]),HH=p[i]/E,QQ=HH*np;
		//assert(QQ>=0);
		if((a[i]-na)%E)return -2;
		exgcd(np,p[i],Ep1,Ep2);
		Ep1=Qx(Ep1,(a[i]-na)/E,HH);
		na+=Qx(np,Ep1,QQ);
		na%=QQ;	
		np=QQ;
	}
	return F+na;
}

int main(){
    freopen("dragon.in","r",stdin);
	freopen("dragon.out","w",stdout);
	int T;
	scanf("%d",&T);
	while(T--){
		scanf("%d%d",&n,&m);M.clear();G=1;
		for(int i=1;i<=n;++i)scanf("%lld",a+i);
		for(int i=1;i<=n;++i){
			scanf("%lld",p+i);
			G*=(p[i]==1);
		}
		for(int i=1;i<=n;++i)scanf("%lld",ne+i);
		for(int i=1,b;i<=m;++i){
			scanf("%d",&b);
			++M[b];
		}
		for(int i=1;i<=n;++i){
			if((M.begin()->ft)>a[i]){
				k[i]=M.begin()->ft;
				--M[M.begin()->ft];
				if((M.begin()->sd)==0)M.erase(M.begin());
			}else{
				++M[a[i]];
				it=M.find(a[i]);
				if((it->sd)>1){
					M[a[i]]-=2;
					k[i]=a[i];
					it=M.find(a[i]);
					if(it->sd==0)M.erase(it);
					++M[ne[i]];
					continue;
				}
				if((it->sd)==1)--it;
				--M[a[i]];
				k[i]=it->ft;
				--M[it->ft];
				if((it->sd)==0)M.erase(it);
				it=M.find(a[i]);
				if(it->sd==0)M.erase(it);
			}
			++M[ne[i]];
		}
		printf("%lld\n",work());
	}
	return 0;
}

```