---
title: 多项式(2)
top: 1
date: 2018-07-17 18:15:28
tags:
	- 多项式
---

船新版本的多项式！加入了

### 开根

$$\large B(x)=\cfrac{A(x)+B_0^2(x)}{2B_0(x)}$$

---

只有开根……

不过这样一道6合一模板题([帕秋莉的超级多项式](http://cogs.pro:8080/cogs/problem/problem.php?pid=2189))还是少见的

卡常卡常卡常卡常

突然开了几个register就过了


代码：

```cpp
#include<bits/stdc++.h>
using namespace std;

#define lg long long
#define MN 1<<19|1
#define lsk 998244353

int n,k,l;
lg pw[MN],inv[MN],rev[MN];
lg Tmpinv[MN],Tmpln[MN],Tmpexp[MN],Tmppow[MN],Tmpfuc[MN],Tmpsqrt[MN],Tmpsqrt2[MN];
lg F[MN],G[MN];

lg Pow(lg A,lg B){
	lg res=1;
	for(;B;B>>=1,A=A*A%lsk)if(B&1)res=res*A%lsk;
	return res;
}

void FFTinit(int l){
	for(int i=1;i<l;++i){
		rev[i]=(rev[i>>1]>>1)|((i&1)*(l>>1));
	}
}

void dft(lg *y,int l){
	for(int i=1;i<l;++i){
		if(i<rev[i])swap(y[i],y[rev[i]]);
	}
	for(int i=1;i<l;i<<=1){
		lg wn=pw[i];
		for(int j=0;j<l;j+=i*2){
			lg w=1;
			for(int k=j;k<i+j;++k){
				lg F=y[k+i]*w%lsk,T=y[k];
				y[k]=(F+T)%lsk;
				y[k+i]=(T-F)%lsk;
				if(y[k+i]<0)y[k+i]+=lsk;
				w=wn*w%lsk;
			}
		}
	}
}

void idft(lg *y,int l){
	reverse(y+1,y+l);
	dft(y,l);lg INv=Pow(l,lsk-2);
	for(int i=0;i<l;++i){
		y[i]=y[i]*INv%lsk;
	}
}

void Inv(lg *y,lg *res,int d){
	if(d!=1){
		Inv(y,res,d+1>>1);
		int l=1<<((int)ceil(log2(d<<1)));
		copy(y,y+d,Tmpinv);
		fill(Tmpinv+d,Tmpinv+l,0);
		FFTinit(l);
		dft(Tmpinv,l);dft(res,l);
		for(int i=0;i<l;++i){
			res[i]=((2-Tmpinv[i]*res[i])%lsk+lsk)*res[i]%lsk;
		}
		idft(res,l);
		fill(res+d,res+l,0);
	}else{
		res[0]=Pow(y[0],lsk-2);
		return;
	}
}

void Der(lg *y,int d){
	for(int i=0;i<d-1;++i){
		y[i]=y[i+1]*(i+1)%lsk;
	}y[d-1]=0;
}

void Int(lg *y,int d){
	for(int i=d-1;i;--i){
		y[i]=y[i-1]*inv[i]%lsk;
	}y[0]=0;
}

void Ln(lg *y,lg *res,int d){
	int l=1<<((int)ceil(log2(d<<1)));
	copy(y,y+d,res);
	fill(res+d,res+l,0);
	Der(res,d);
	fill(Tmpln,Tmpln+l,0);
	Inv(y,Tmpln,d);FFTinit(l);
	dft(Tmpln,l);dft(res,l);
	for(int i=0;i<l;++i){
		res[i]=res[i]*Tmpln[i]%lsk;
	}
	idft(res,l);
	Int(res,d);
	fill(res+d,res+l,0);
}

void Exp(lg *y,lg *res,int d){
	if(d!=1){
		int l=1<<((int)ceil(log2(d<<1)));
		Exp(y,res,d+1>>1);
		fill(Tmpexp,Tmpexp+l,0);
		Ln(res,Tmpexp,d);
		for(int i=0;i<d;++i){
			Tmpexp[i]=y[i]-Tmpexp[i];
			if(Tmpexp[i]<0)Tmpexp[i]+=lsk;
		}++Tmpexp[0];
		if(Tmpexp[0]>=lsk)Tmpexp[0]-=lsk;
		FFTinit(l);
		dft(res,l);dft(Tmpexp,l);
		for(int i=0;i<l;++i){
			res[i]=res[i]*Tmpexp[i]%lsk;
		}
		idft(res,l);
		fill(res+d,res+l,0);
	}else{
		res[0]=1;
		return;
	}
}

void Pow(lg *y,lg *res,int d,lg k){
	int l=1<<((int)ceil(log2(d<<1)));
	fill(Tmppow,Tmppow+d,0);
	Ln(y,Tmppow,d);k%=lsk;
	for(int i=0;i<l;++i)Tmppow[i]=Tmppow[i]*k%lsk;
	Exp(Tmppow,res,d);
	fill(res+d,res+l,0);
}

void Sqrt(lg *y,lg *res,int d){
	if(d!=1){
		int l=1<<((int)ceil(log2(1<<d)));
		Sqrt(y,res,(d+1)>>1);
		fill(Tmpsqrt,Tmpsqrt+l,0);
		Inv(res,Tmpsqrt,d);
		fill(Tmpsqrt+d,Tmpsqrt+l,0);
		copy(y,y+d,Tmpsqrt2);
		fill(Tmpsqrt2+d,Tmpsqrt2+l,0);
		FFTinit(l);
		dft(Tmpsqrt,l);dft(Tmpsqrt2,l);dft(res,l);
		for(int i=0;i<l;++i){
			res[i]=res[i]+Tmpsqrt[i]*Tmpsqrt2[i]%lsk;
			if(res[i]>=lsk)res[i]-=lsk;
		}
		idft(res,l);
		for(int i=0;i<d;++i){
			res[i]=res[i]*inv[2]%lsk;
		}
		fill(res+d,res+l,0);
	}else{
		res[0]=(lg)(sqrt(y[0]));
	}
}

void init(){
	pw[0]=inv[0]=inv[1]=1;
	for(int i=2;i<=1<<19;++i){
		pw[i]=Pow(3,(lsk-1)/i/2);
		inv[i]=inv[lsk%i]*(lsk-lsk/i)%lsk;
	}
}

int main(){
	freopen("polynomial.in","r",stdin);
	freopen("polynomial.out","w",stdout);
	lg k;
	scanf("%d%lld",&n,&k);
	init();
	for(int i=0;i<n;++i)scanf("%lld",F+i);
	int l=1<<((int)ceil(log2(1<<n)));
	Sqrt(F,G,n);
	//for(int i=0;i<n;++i)printf("%lld ",G[i]);
	fill(F,F+l,0);
	Inv(G,F,n);
	fill(G,G+l,0);
	Int(F,n);
	Exp(F,G,n);
	fill(F,F+l,0);
	Inv(G,F,n);++F[0];
	fill(G,G+l,0);
	Ln(F,G,n);++G[0];
	fill(F,F+l,0);
	Pow(G,F,n,k);
	Der(F,n);
	for(int i=0;i<n;++i)printf("%lld ",F[i]);
	return 0;
}
```
