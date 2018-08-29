---
title: 虚树
date: 2018-07-16 02:21:10
tags: 
	- 图论
top: 1
---

##转移重要代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define MN 600005
#define pil pair<int,long long>
#define mp make_pair
#define ft first
#define sd second
#define lg long long
int n,m,K,q;
int nex[MN],fr[MN],vi[MN];lg wi[MN];int tote;
int tail=0,stk[MN];
void add(int x,int y,lg z){
	nex[++tote]=fr[x];fr[x]=tote;wi[tote]=z;vi[tote]=y;
}
int dfc=0,dfn[MN];int dep[MN],f[20][MN];lg g[20][MN];
void dfs(int x,int fa){
	dfn[x]=++dfc;for(int i=1;i<=19;++i)f[i][x]=f[i-1][f[i-1][x]],g[i][x]=min(g[i-1][x],g[i-1][f[i-1][x]]);
	for(int i=fr[x];i;i=nex[i])if(vi[i]!=fa){
		f[0][vi[i]]=x;g[0][vi[i]]=wi[i];dep[vi[i]]=dep[x]+1;dfs(vi[i],x);
	}
}
pil Lca(int x,int y){
	if(dep[x]<dep[y])swap(x,y);
	if(x==y)return mp(x,0);
	lg res=0x3f3f3f3f3f3f3f3f;
	for(int i=19;~i;--i){
		if(dep[f[i][x]]>=dep[y]){
			res=min(res,g[i][x]);
			x=f[i][x];
		}
	}if(x==y) return mp(x,res);
	for(int i=19;~i;--i){
		if(f[i][x]!=f[i][y]){
			res=min(res,min(g[i][x],g[i][y]));
			x=f[i][x];y=f[i][y];
		}
	}
	return mp(f[0][x],min(res,min(g[0][x],g[0][y])));
}
int v[MN],dd[MN];
int Fr[MN],Nex[MN],Vi[MN];lg Wi[MN];int Tot=0;
queue<int> Q;
void Add(int x,int y,lg z){Nex[++Tot]=Fr[x];Wi[Tot]=z;Fr[x]=Tot;Vi[Tot]=y;Nex[++Tot]=Fr[y];Wi[Tot]=z;Fr[y]=Tot;Vi[Tot]=x;}
bool cmp(int x,int y){
	return dfn[x]<dfn[y];
}
lg dfs2(int x,int fa){
	lg ans=0;
	// cout<<x<<' '<<fa<<endl;
	for(int i=Fr[x];i;i=Nex[i])if(Vi[i]!=fa){
		// cout<<Vi[i]<<endl;
		if(dd[Vi[i]])ans+=Wi[i];else ans+=min(Wi[i],dfs2(Vi[i],x));
	}return ans;
}
void bt(){
	sort(v+1,v+m+1,cmp);Tot=0;pil L;tail=0;
	for(int i=0;i<=m;++i)dd[v[i]]=1;
	// cout<<"&"<<endl;
	for(int i=0;i<=m;++i){
		// cout<<i<<endl;
		if(i&&v[i]==v[i-1])continue;
		// cout<<v[i]<<' '<<v[i-1]<<endl;
		L=Lca(stk[tail],v[i]);;
		while(tail>1){
			if(dep[L.ft]>dep[stk[tail]]||L.ft==stk[tail])break;
			// cout<<stk[tail]<<' '<<stk[tail-1]<<' '<<Lca(stk[tail],stk[tail-1]).sd<<"&&&"<<endl;
			Add(stk[tail],stk[tail-1],Lca(stk[tail],stk[tail-1]).sd);
			--tail;
		}
		if(L.ft!=stk[tail])Fr[L.ft]=0,stk[++tail]=L.ft,Q.push(L.ft);
		for(int i=0;i<=m;++i)dd[v[i]]=0;
		Fr[v[i]]=0;stk[++tail]=v[i];Q.push(v[i]);
	}
	// cout<<"@"<<endl;
	while(tail!=1)Add(stk[tail],stk[tail-1],Lca(stk[tail],stk[tail-1]).sd),--tail;
	printf("%lld\n",dfs2(1,1));
	while(!Q.empty()){int x=Q.front();Q.pop();dd[x]=0;}
	// cout<<"^"<<endl;
}
int main(){
	scanf("%d",&n);
	for(int i=1,x,y;i<n;++i){
		lg z;
		scanf("%d%d%lld",&x,&y,&z);
		add(x,y,z);add(y,x,z);
	}dep[1]=1;dfs(1,1);
	scanf("%d",&q);v[0]=1;
	while(q--){
		// cout<<"$"<<endl;
		scanf("%d",&m);
		for(int i=1;i<=m;++i)scanf("%d",v+i);
		bt();
	}
	return 0;
}

```
