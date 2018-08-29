---
title: 多项式(1)
date: 2018-07-16 12:46:48
tags:
	- 多项式
top: 1
mathjax: true
---

学习了一发多项式，感觉码风得到改善

包括了：

### 1. 求逆

$$\large B(x)=2B_0(x)-A(x)B_0^2(x)$$

### 2. 求导

xjb推即可

### 3. 积分

xjb推即可

### 4. ln

$$\large \begin{aligned}由：
& H(x)=G(B(x))=B'(x)*G(B(x))\\
& H'(x)=G'(B(x))*B'(x)\\
可得：
& ln(B(x))=\dfrac{1}{B(x)}*B'(x)\end{aligned}$$

### 5. exp

$$\large B(x)=B_0(x)*(1-ln(B_0(x))+A(x))$$

### 6. pow

$$\large B(x)^k=e^{ln(B(x))\times k}$$

[水果沙拉(CodeChef)](https://www.codechef.com/JULY18A/problems/PFRUIT)

基本思路就是将k次方多项式展开，对每一项单独计算，可以推导出是若干以e为幂的数的和的乘积，然后就是多项式了

```cpp
#include<bits/stdc++.h>
using namespace std;

#define lg long long
#define MN 1<<19|1
#define lsk 998244353

int n,k,l;
lg pw[MN],inv[MN],rev[MN],Ex[MN],Iex[MN];
lg Tmpinv[MN],Tmpln[MN],Tmpexp[MN],Tmppow[MN],Tmpfuc[MN];
lg F[MN],G[MN],Res[MN];

lg Pow(lg A,lg B){
	lg res=1;
	for(;B;B>>=1,A=A*A%lsk)if(B&1)res=res*A%lsk;
	return res;
}

void FFtinit(int l){
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
				w=w*wn%lsk;
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
	if(d==1){
		res[0]=Pow(y[0],lsk-2);
		return;
	}else{
		Inv(y,res,(d+1)>>1);
		int l=1<<((int)ceil(log2(d<<1)));
		copy(y,y+d,Tmpinv);
		fill(Tmpinv+d,Tmpinv+l,0);
		FFtinit(l);
		dft(Tmpinv,l);dft(res,l);
		for(int i=0;i<l;++i){
			res[i]=((2-Tmpinv[i]*res[i])%lsk+lsk)*res[i]%lsk;
		}
		idft(res,l);
		fill(res+d,res+l,0);
	}
}

void Der(lg *y,int d){
	for(int i=0;i<d-1;++i){
		y[i]=y[i+1]*(i+1)%lsk;
	}
	y[d-1]=0;
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
	Inv(y,Tmpln,d);FFtinit(l);
	dft(Tmpln,l);dft(res,l);
	for(int i=0;i<l;++i){
		res[i]=res[i]*Tmpln[i]%lsk;
	}
	idft(res,l);
	Int(res,d);
	fill(res+d,res+l,0);
}

void Exp(lg *y,lg *res,int d){
	if(d==1){
		res[0]=1;
		return;
	}else{
		int l=1<<((int)ceil(log2(d<<1)));
		Exp(y,res,(d+1)>>1);
		fill(Tmpexp,Tmpexp+l,0);
		Ln(res,Tmpexp,d);
		for(int i=0;i<d;++i){
			Tmpexp[i]=y[i]-Tmpexp[i];
			if(Tmpexp[i]<0)Tmpexp[i]+=lsk;
		}
		++Tmpexp[0];
		if(Tmpexp[0]>=lsk)Tmpexp[0]-=lsk;
		FFtinit(l);
		dft(res,l);dft(Tmpexp,l);
		for(int i=0;i<l;++i){
			res[i]=res[i]*Tmpexp[i]%lsk;
		}
		idft(res,l);
		fill(res+d,res+l,0);
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

void Func(lg u,lg *res){
	Tmpfuc[1]=u+1;
	Tmpfuc[0]=0;
	int l=1<<((int)ceil(log2((k+1)<<1)));
	fill(res,res+l,0);
	Exp(Tmpfuc,res,k+2);
	for(int i=0;i<=k;++i)res[i]=res[i+1];
	res[k+1]=0;
	fill(res+k+1,res+l,0);
	FFtinit(l);
	dft(res,l);
	for(int i=0;i<=l;++i){
		res[i]=res[i]*Iex[i]%lsk;
	}
	idft(res,l);
	fill(res+k+1,res+l,0);
	fill(Tmpfuc,Tmpfuc+l,0);
}

void init(){
	l=1<<((int)ceil(log2((k+1)<<1)));
	pw[0]=1;inv[0]=inv[1]=1;
	for(int i=2;i<=1<<19;++i){
		pw[i]=Pow(3,(lsk-1)/i/2);
		inv[i]=inv[lsk%i]*(lsk-lsk/i)%lsk;
		assert(inv[i]);
	}
	Ex[0]=1;
	for(int i=1;i<=k;++i){
		Ex[i]=Ex[i-1]*inv[i+1]%lsk;
	}
	Inv(Ex,Iex,k+1);
	FFtinit(l);
	dft(Iex,l);
}

int main(){
	scanf("%d%*d%d",&k,&n);
	init();
	fill(Res,Res+l,0);Res[0]=1;
	for(int i=1;i<=n;++i){
		lg a,b;int c;
		scanf("%lld%lld%d",&a,&b,&c);
		fill(G,G+k+1,0);
		fill(F,F+k+1,0);
		Func(b,G);Func(a-1,F);
		for(int j=0;j<=k;++j){
			G[j]-=F[j];
			if(G[j]<0)G[j]+=lsk;
		}
		fill(F,F+k+1,0);
		Pow(G,F,k+1,c);
		FFtinit(l);
		dft(Res,l);dft(F,l);
		for(int j=0;j<l;++j){
			Res[j]=Res[j]*F[j]%lsk;
		}
		idft(Res,l);
		fill(Res+k+1,Res+l,0);
	}
	lg res=1;
	for(int i=2;i<=k;++i)res=res*i%lsk;
	printf("%lld",res*Res[k]%lsk);
	return 0;
}
```

