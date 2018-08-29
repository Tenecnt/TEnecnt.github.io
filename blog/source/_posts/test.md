---
title: NOI2018归程(return)
top: 1
date: 2018-07-19 07:30:38
tags:
	- NOI
	- 图论
	- 生成树
---
[~~题面~~](https://judge-duck.online:10086/problem/noi18a)

dfsdfsdfdfsadf
在同步赛中，时间都花在T2上了，导致这题没怎么想，看到暴力+离线有75就马上打了，没有任何向标算上的思考，当然，可持久化并查集还是想到的，但是写……

不过话说回来，写log^2的可持久化并查集的都过了……

不过，标算是个神奇的东西，叫：

## 克鲁斯卡尔重构树

## ～～其实这是个很蠢的东西～～

大家都会克鲁斯卡尔最小生成树吧

只要在建树的时候，为新加的边新建一个点，并把别的两个点连它的儿子

这样，若干次的联通块就是若干子树了，只要倍增维护子树最小值就行了

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<queue>
using namespace std;

#define MN 800005
#define lg long long
#define pli pair<lg,int>
#define mp make_pair
#define ft first
#define sd second

int n,m;

int read(){
	int d=0,f=1;char c=getchar();
	while(c>'9'||c<'0'){if(c=='-')f=-f;c=getchar();}
	while(c>='0'&&c<='9'){d=d*10+c-'0';c=getchar();}
	return d*f;
}

int nex[MN],tot=0,vi[MN],fr[MN],ai[MN];
lg wi[MN];
void add(int x,int y,lg z,int a){
	nex[++tot]=fr[x];fr[x]=tot;vi[tot]=y;wi[tot]=z;ai[tot]=a;
}

struct Ege{
	int x,y,a;lg c;
}E[MN];
bool operator<(Ege A,Ege B){
	return A.a<B.a;
}

struct Qry{
	int v,p,id;
}Q[MN];
bool operator<(Qry A,Qry B){
	return A.p<B.p;
}

priority_queue<pli,vector<pli >,greater<pli > > pQ;
lg ds[MN];int vis[MN],F[MN];lg V[MN];int L[MN];
void djk(){
	memset(ds,0x3f,sizeof ds);
	memset(vis,0,sizeof vis);
	ds[1]=0;
	while(!pQ.empty())pQ.pop();
	pQ.push(mp(0,1));
	while(!pQ.empty()){
		int x=pQ.top().sd;pQ.pop();
		if(vis[x])continue;vis[x]=1;
		for(int i=fr[x];i;i=nex[i]){
			if(ds[x]+wi[i]<ds[vi[i]]){
				ds[vi[i]]=ds[x]+wi[i];
				pQ.push(mp(ds[vi[i]],vi[i]));
			}
		}
	}
	for(int i=1;i<=n;++i)F[i]=i,V[i]=ds[i],L[i]=0x7f7f7f7f;
}

int gf(int x){
	return F[x]==x?x:F[x]=gf(F[x]);
}

lg res[MN];
int f[20][MN];
lg Lasans,Ans=0;

void clean(){
	tot=0;memset(fr,0,sizeof fr);Lasans=0;
}

void un(int x,int y,int v){
	f[0][x]=++n;
	f[0][y]=n;
	V[n]=min(V[x],V[y]);
	L[n]=v;
	F[x]=F[y]=n;F[n]=n;
}

void bt(){
	sort(E+1,E+m+1);
	for(int i=m;i;--i){
		int fx=gf(E[i].x),fy=gf(E[i].y);
		if(fx!=fy)un(fx,fy,E[i].a);
	}
	for(int i=1;i<=19;++i){
		for(int j=1;j<=n;++j){
			f[i][j]=f[i-1][f[i-1][j]];
		}
	}
}

int main(){
	int T=read();
	while(T--){
		clean();
		n=read();m=read();
		for(int i=1;i<=m;++i){
			int a=read(),b=read();lg c=read(),d=read();
			add(a,b,c,d);add(b,a,c,d);E[i]=(Ege){a,b,d,c};
		}int nn=n;
		djk();Lasans=0;bt();
		int q=read(),K=read(),S=read();
		for(int i=1;i<=q;++i){
			int v=(read()+K*Lasans-1)%nn+1,
				p=(read()+K*Lasans)%(S+1);
			for(int j=19;~j;--j)if(L[f[j][v]]>p)v=f[j][v];
			Lasans=V[v];
			printf("%lld\n",Lasans);
		}
	}
	return 0;
}
```
