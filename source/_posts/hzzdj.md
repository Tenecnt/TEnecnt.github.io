---
title: 后缀自动机
date: 2018-07-16 02:26:05
tags:
	- 字符串
top: 1
---

##转移重要代码
```cpp
char c[MN];
int n,tot=0;
struct Node{
	int maxlen,slink,tran[26];
}N[MN];
int Nd(int _maxlen,int _tran,int _slink){
	N[++tot]=(Node){_maxlen,_slink,0};
	if(_tran){
		for(int i=0;i<26;++i)if(N[_tran].tran[i])N[tot].tran[i]=N[_tran].tran[i];else N[tot].tran[i]=0;
	}else for(int i=0;i<26;++i)N[tot].tran[i]=0;
	return tot;
}
int addchar(char cc,int u){
	int ch=cc-'a';
	int z=Nd(N[u].maxlen+1,0,0);
	int v=u;
	while(v&&!N[v].tran[ch]){
		N[v].tran[ch]=z;
		v=N[v].slink;
	}
	if(!v){N[z].slink=1;return z;}
	int x=N[v].tran[ch];
	if(N[v].maxlen==N[x].maxlen-1){
		N[z].slink=x;
		return z;
	}
	int y=Nd(N[v].maxlen+1,x,N[x].slink);
	N[x].slink=y;N[z].slink=y;
	while(v&&N[v].tran[ch]==x){
		N[v].tran[ch]=y;
		v=N[v].slink;
	}return z;
}
void insert(){
	Nd(0,0,0);
	int now=1;
	for(int i=1;i<=n;++i){now=addchar(c[i],now);}
}
```
