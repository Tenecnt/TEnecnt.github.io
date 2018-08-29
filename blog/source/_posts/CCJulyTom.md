---
title: (CC JulyLong)Tom and Jerry
top: 1
mathjax: true
date: 2018-07-24 19:54:37
tags:
	- 图论
	- 最大团
---
这道题首先先猜一个结论：这是要求最大团

但是最大团怎么跑200000呢

这里有一个优越的性质：这是一个弦图

弦图有一个优秀的性质：它有一个完美消除序列

完美消除序列是一个序列，其中每一项在所有往后的项构成的图中都是关键点

还有一个性质：它的最大团大小就是他的最大染色数

还有一个性质：按逆完美消除序列操作贪心染色就是最优解

所以求出完美消除序列就行了

于是要用一个神奇的算法，它叫MCS

它倒过来求完美消除序列

每次取与现在已被取的点中度数最大的

弄一堆链表~~(vector)~~就能操作了

# 以上所有内容，全部只会用不会证。证明？不存在的～～

留下一个[好东西](https://www.codechef.com/JULY18A/problems/JERRYTOM)
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MN 400005

int fr[MN],nex[MN<<1],vi[MN<<1],tot=0;
void add(int x,int y){
	nex[++tot]=fr[x];fr[x]=tot;vi[tot]=y;
}

int n,m;
int vd[MN],d[MN];

struct Node{
	int v,id;
};
bool operator<(Node A,Node B){
	return A.v<B.v||A.v==B.v&&A.id<B.id;
}

vector<int> V[MN];

void clear(){
	memset(fr,0,sizeof fr);
	tot=0;
	memset(vd,0,sizeof vd);
	memset(d,0,sizeof d);
	for(int i=0;i<=n;++i)V[i].clear();
}

int main(){
	int T;
	scanf("%d",&T);
	while(T--){
		clear();
		scanf("%d%d",&n,&m);
		for(int i=1,a,b;i<=m;++i){
			scanf("%d%d",&a,&b);
			add(a,b);add(b,a);
		}
		for(int i=1;i<=n;++i){
			V[0].push_back(i);
		}int Ms=0;int res=0;
		for(int i=1;i<=n;++i){
			while(V[Ms].size()==0||vd[V[Ms][V[Ms].size()-1]]){
				if(V[Ms].size()==0)--Ms;
				else V[Ms].pop_back();
			}
			int x=V[Ms][V[Ms].size()-1],sz=1;
			V[Ms].pop_back();
			vd[x]=1;
			//cout<<x<<"#"<<endl;
			for(int i=fr[x];i;i=nex[i])if(!vd[vi[i]]){
				++d[vi[i]];
				V[d[vi[i]]].push_back(vi[i]);
				Ms=max(Ms,d[vi[i]]);
				//Q.push((Node){d[vi[i]],vi[i]});
			}else ++sz;
			res=max(res,sz);
			//cout<<sz<<endl;
		}
		printf("%d\n",res);
	}
	return 0;
}
```
