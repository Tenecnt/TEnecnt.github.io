---
title: Cayley-Hamilton学习
top: 1
mathjax: true
date: 2018-10-17 20:56:59
tags:
	- 数列
	- 递推式
---

为了配合BM,学习了CH

CH太dark了

首先,我们有一个递推式

然后...就[做](https://www.lydsy.com/JudgeOnline/problem.php?id=4161)完了
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
#define MN 60005
#define Md 1000000007
#define poly vector<int>


int n;lg k;
namespace{
    inline int A(int x,int y){return x+y>=Md?x+y-Md:x+y;}
    inline int S(int x,int y){return x-y<0?x-y+Md:x-y;}
 	inline void _A(int &x,int y){x=A(x,y);}
    inline int M(int x,int y){return (lg)x*y%Md;}
    inline int P(int x,int y){int res=1;for(;y;y>>=1,x=M(x,x))if(y&1)res=M(res,x);return res;}
    inline int D(int x,int y){return M(x,P(y,Md-2));}
}
namespace Lamp{
	int dta[MN],n;
	poly bm(poly s){
		static poly now,lst,llst;
		now.clear();lst.clear();llst.clear();
		int fail=-1,lfail=-1,cnt=0;
		for(int i=0;i<n;++i){
			int sum=S(0,s[i]);
			for(unsigned j=0;j<now.size();++j){
				_A(sum,M(now[j],s[i-j-1]));
			}
			dta[i]=sum;
			if(!sum)continue;
			fail=i;
			if(!cnt){
				lfail=fail;++cnt;now.resize(i+1);continue;
			}
			int ml=D(S(0,dta[i]),dta[lfail]);
			++cnt;lst.swap(now);
			now.clear();now.resize(i-lfail-1);
			now.pb(S(0,ml));
			for(unsigned j=0;j<llst.size();++j){
				now.pb(M(ml,llst[j]));
			}
			if(now.size()<lst.size())now.resize(lst.size());
			for(unsigned j=0;j<lst.size();++j){
				_A(now[j],lst[j]);
			}
			if(i-lfail+llst.size()>=lst.size())llst=lst,lfail=fail;
		}
		return now;
	}
}

poly s,res;
poly g;

poly Mul(poly A,poly B){
	static poly res;res.clear();
	res.resize(A.size()+B.size()-1);
	for(unsigned i=0;i<A.size();++i){
		for(unsigned j=0;j<B.size();++j){
			_A(res[i+j],M(A[i],B[j]));
		}
	}
	return res;
}

namespace Gerr{
	int n;
	poly Mul(poly A,poly B){
		static poly res;res.clear();
		res.resize(n*2-1);
		for(unsigned i=0;i<A.size();++i){
			for(unsigned j=0;j<B.size();++j){
				_A(res[i+j],M(A[i],B[j]));
			}
		}
		for(int i=n+n-2;i>=n;--i){
			for(int j=0;j<n;++j){
				_A(res[i-j-1],M(res[i],::res[j]));
			}
		}
		res.resize(n);
		return res;
	}
	poly init(int d,lg x){
		n=d;
		poly g,h;
		h.resize(n);h[1]=1;g.resize(n);g[0]=1;
		for(;x;x>>=1,h=Mul(h,h))if(x&1)g=Mul(g,h);
		return g;
	}
}

int main(){
	read(k);read(n);
	for(int i=0,x;i<n;++i)read(x),_A(x,Md),res.pb(x);
	for(int i=0,x;i<n;++i)read(x),_A(x,Md),s.pb(x);
	if(k<=n)return printf("%d",s[k-1]),0;
	g=Gerr::init(res.size(),k);
	int Res=0;
	for(unsigned i=0;i<res.size();++i)_A(Res,M(s[i],g[i]));
	cout<<Res;
	return 0;
}
```
(以上为BM(Lampard)+CH(Gerrard))

还是说明一下CH吧(尽管BM的说明被咕咕咕了)

当然,定理本身是证不出来的,这辈子都证不出来的

就是这么个东西

$$ C^{n} =\sum_{0}^{n-1}B_{n-i}C^{i} $$

然后么......多项式取模一下(用$$x^k$$取模$$B$$)就知道$$C^{k}$$等价的用$$C^{0}..C^{n-1}$$表示的多项式了

然后乘起来就好了
