---
title: Suffix Automaton重学
top: 1
mathjax: true
date: 2018-10-18 20:43:24
tags:
	- 字符串
	- 后缀自动机
	- suffix
---

重新学习了sam

对于sam的建造过程和证明就不讲了,只贴板子

```cpp
namespace Yuuki{
	int last,tot;
	struct Node{
		int slink,len,tran[26];
		int MAX,MIN,cnt;
		Node(int _min=0,int _max=0,int _cnt=0){
			memset(tran,-1,sizeof tran);
			slink=0;len=0;
			MAX=_max,MIN=_min,cnt=_cnt;
		}
	}N[MN];

	vector<int> Sons[MN];

	void add(char C,int v){
		int c=C-'a';
		int z=tot++,l=last;
		N[z]=Node(v,v,1);
		N[z].len=N[l].len+1;
		last=z;
		while((~l)&&N[l].tran[c]==-1){
			N[l].tran[c]=z;
			l=N[l].slink;
		}
		if(l==-1)return (void)(N[z].slink=0);
		int g=N[l].tran[c];
		if(N[l].len+1==N[g].len)return (void)(N[z].slink=g);
		int y=tot++;
		N[y]=N[g];
		N[y].len=N[l].len+1;
		N[y].cnt=0;N[y].MAX=-0x3f3f3f3f;N[y].MIN=0x3f3f3f3f;
		N[g].slink=N[z].slink=y;
		while((~l)&&N[l].tran[c]==g){
			N[l].tran[c]=y;
			l=N[l].slink;
		}
	}

	void build(){
		for(int i=1;i<=n;++i)add(c[i],a[i]);
		for(int i=1;i<tot;++i)Sons[N[i].slink].pb(i);
	}
}

```

再来一道[模板题](https://loj.ac/problem/2133)

大致就是求出每个长度的相同子串对个数以及这些对中起始位权值乘积最大值

做法就是反转串,然后在slink树上找子树最大乘积与子树个数和,最后前缀和差分处理即可

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

#define LLLINE cerr<<"@@@@@@@@@@@@@@@@@@@@"<<endl

template <class _T_>
void read(_T_& d){
	d=0;int f=1;char c=getchar();
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f*=-1;
	for(;c>='0'&&c<='9';c=getchar())d=d*10+c-'0';
	d*=f;
}

/************************************************/
#define MN 600005
int n;
char c[MN];
int a[MN];
lg res[MN],cnt[MN];
namespace Yuuki{
	int last,tot;
	struct Node{
		int slink,len,tran[26];
		int MAX,MIN,cnt;
		Node(int _min=0,int _max=0,int _cnt=0){
			memset(tran,-1,sizeof tran);
			slink=0;len=0;
			MAX=_max,MIN=_min,cnt=_cnt;
		}
	}N[MN];

	vector<int> Sons[MN];

	void add(char C,int v){
		int c=C-'a';
		int z=tot++,l=last;
		N[z]=Node(v,v,1);
		N[z].len=N[l].len+1;
		last=z;
		while((~l)&&N[l].tran[c]==-1){
			N[l].tran[c]=z;
			l=N[l].slink;
		}
		if(l==-1)return (void)(N[z].slink=0);
		int g=N[l].tran[c];
		if(N[l].len+1==N[g].len)return (void)(N[z].slink=g);
		int y=tot++;
		N[y]=N[g];
		N[y].len=N[l].len+1;
		N[y].cnt=0;N[y].MAX=-0x3f3f3f3f;N[y].MIN=0x3f3f3f3f;
		N[g].slink=N[z].slink=y;
		while((~l)&&N[l].tran[c]==g){
			N[l].tran[c]=y;
			l=N[l].slink;
		}
	}

	void build(){
		for(int i=1;i<=n;++i)add(c[i],a[i]);
		for(int i=1;i<tot;++i)Sons[N[i].slink].pb(i);
	}

	void init(){
		last=0;tot=1;
		N[0]=Node(0x3f3f3f3f,-0x3f3f3f3f,0);
		N[0].slink=-1;
	}

#define chmax(x,y) (x<y)?(x=y):0
#define chmin(x,y) (x>y)?(x=y):0
	void dfs(int x){
		for(int i:Sons[x]){
			dfs(i);
			if(N[x].cnt>=1)chmax(res[N[x].len],1ll*N[i].MAX*N[x].MAX);
			if(N[x].cnt>=1)chmax(res[N[x].len],1ll*N[i].MIN*N[x].MIN);
			N[x].cnt+=N[i].cnt;
			chmax(N[x].MAX,N[i].MAX);
			chmin(N[x].MIN,N[i].MIN);
		}
		cnt[N[x].len]+=1ll*N[x].cnt*(N[x].cnt-1)/2;
		if(x&&N[N[x].slink].len>=0)cnt[N[N[x].slink].len]-=1ll*N[x].cnt*(N[x].cnt-1)/2;;
	}

	void main(){
		dfs(0);
		for(int i=n-2;~i;--i){
			chmax(res[i],res[i+1]);
			cnt[i]+=cnt[i+1];
			if(!cnt[i+1])res[i+1]=0;
		}
		if(!cnt[0])res[0]=0;
	}
}
int main(){
	memset(res,-0x3f,sizeof res);
	read(n);
	scanf("%s",c+1);
	reverse(c+1,c+n+1);
	for(int i=1;i<=n;++i)read(a[i]);
	reverse(a+1,a+n+1);
	Yuuki::init();
	Yuuki::build();
	Yuuki::main();
	for(int i=0;i<n;++i){
		printf("%lld %lld\n",cnt[i],res[i]);
	}
	return 0;
}
```
