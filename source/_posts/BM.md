---
title: Berlekamp-Massey学习
top: 1
mathjax: true
date: 2018-10-06 18:12:03
tags:
	- 数列
	- 递推式
---

刚刚学习了BM算法,用来求解一个数列所对应的最小次数递推式

它的复杂度是$$O(n^2)$$的

它的思路并不复杂

首先,我们有一个数列

接下来,我们要构造一个可以符合这个数列的递推式res,对于所有位置

$$\sum_{1}^{q}a[i-q] \times res[q]=0$$

我们从左往右扫

每个位置记录$$ delta[i] $$表示$$\sum_{1}^{q}a[i-q] \times res[q]$$

当$$delta[i]=0$$时,显然不需要任何修改

否则,我们对递推式进行一些修改使其符合数列的前$$i$$位

修改方法比较玄学......我不想讲了

```cpp
#include<bits/stdc++.h>
using namespace std;

#define mp make_pair
#define pb push_back
#define lg long long
#define pii pair<lg,lg>
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
#define Md 998244353
#define MN 5005
#define poly vector<int>
namespace{
    inline int A(int x,int y){return x+y>=Md?x+y-Md:x+y;}
    inline int S(int x,int y){return x-y<0?x-y+Md:x-y;}
    inline int M(int x,int y){return (lg)x*y%Md;}
    inline int P(int x,int y){int res=1;for(;y;y>>=1,x=M(x,x))if(y&1)res=M(res,x);return res;}
    inline int D(int x,int y){return M(x,P(y,Md-2));}
}
poly s;int n;
int dta[MN];
poly bm(poly s){
    static poly now,lst,llst;
    now.clear();lst.clear();llst.clear();
    int fail=-1,lfail=-1,cnt=0;
    for(int i=0;i<n;++i){
        int sum=S(0,s[i]);
        for(unsigned j=0;j<now.size();++j){
            sum=A(sum,M(now[j],s[i-j-1]));
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
            now[j]=A(now[j],lst[j]);
        }
        if(i-lfail+llst.size()>=lst.size())llst=lst,lfail=fail;
    }
    return now;
}
int main(){
    read(n);
    s.resize(n);
    for(int i=0;i<n;++i)read(s[i]);
    poly res=bm(s);
    cerr<<res.size()<<endl;
    for(auto z:res)printf("%d ",z);
    return 0;
}
```
