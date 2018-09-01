---
title: lct学习
top: 1
mathjax: true
date: 2018-09-01 18:06:29
tags:
	- 数据结构
	- 图论
	- lct
---
终于还是学习了一下lct~~(然而我还是不会证复杂度)~~

讲道理，这东西比想象的要来的简单

## 概述

首先，这个东西是用来维护树的信息的

可以支持link和cut操作(所以叫做link-cut-tree)

这个link和cut，顾名思义，就是把图断开和接上

如何做到呢？

同是用数据结构维护一树，我们会想到树剖

树剖分重链轻链，这里也可以这样

但略有偏差，这里重链不一定要最大，而且一个点向下可以没有重链

但重链与轻链随时会改变，那怎么办呢

我们不用线段树维护，我们用splay

支持分裂合并的平衡树都可以完成这件事

但为什么选splay？因为出于某种玄学的复杂度证明用splay是一个log的……

现在，我们用一棵splay表示一条重链，中序遍历表示它从上往下的顺序

那么每次如何操作呢？

我们需要制造出这么几个操作：

###is_root

它的意思就是判断一个点是否是一棵splay的根

注意这是splay的根，而不是树的根~~我被坑了一次~~

注意这个函数没有在其他splay出现过，这里只是为了不用写分裂合并

方法很简单，用“父亲有没有孩子是自己”来标记是否为根

这使lct中的splay短了很多

```cpp
    bool Isroot(int x){
        return N[N[x].fa][0]!=x&&N[N[x].fa][1]!=x;
    }
```

### access

它指打通一个点到根的链(打成重链)
就是不停spaly，然后把父亲的儿子变成自己，很好理解
```cpp
    void Access(int x){
        for(int i=0;x;i=x,x=N[x].fa){
            Splay(x);N[x][1]=i;Up(x);//Up->上传标记
        }
    }
```

### mkroot

把一个点变成根(树的根)
有了access，然后splay它他就成了splay的根了，但实际中序遍历还是最后
那再reverse一下就好了啊
```cpp
    void Mkroot(int x){
        Access(x);Splay(x);N[x].fl^=1;
    }
```
---
还有一些零碎操作比如findroot什么的，很好想清楚，就不讲了

## 题

### [弹飞绵羊](https://www.lydsy.com/JudgeOnline/problem.php?id=2002)

明显每次把儿子a从b接到c上，没有什么困难的东西
```cpp
#include<bits/stdc++.h>
using namespace std;
 
#define MN 400005
 
int v[MN];
int n;
 
namespace CFA{//China Football Association!!
    struct Node{
        int fa,s[2],fl,sz;
        int& operator[](int x){return s[x];}
    }N[MN];
 
    bool Isroot(int x){
        return N[N[x].fa][0]!=x&&N[N[x].fa][1]!=x;
    }
 
    void Up(int x){
        N[x].sz=N[N[x][0]].sz+N[N[x][1]].sz+1;
    }
 
    void Down(int x){
        if(!N[x].fl)return;
        swap(N[x][1],N[x][0]);
        N[N[x][0]].fl^=1;
        N[N[x][1]].fl^=1;
        N[x].fl=0;
    }
 
    void Rotate(int x,int y=0){
        int p=N[x].fa,f=N[p][0]==x,pp=N[p].fa;
        N[p][f^1]=N[x][f];
        N[N[x][f]].fa=p;
        N[x][f]=p;N[p].fa=x;
        Up(p);Up(x);
        if(N[pp][0]==p)N[pp][0]=x;
        if(N[pp][1]==p)N[pp][1]=x;
        N[x].fa=pp;
        if(y)Rotate(y);
    }
 
    int stk[MN],ssz=0;
    void Splay(int x){
        assert(ssz==0);
        stk[++ssz]=x;
        for(int i=x;!Isroot(i);i=N[i].fa)stk[++ssz]=N[i].fa;
        while(ssz)Down(stk[ssz--]);
        while(!Isroot(x)){
            int p=N[x].fa,f=N[p][1]==x,pp=N[p].fa,ff=N[pp][1]==p;
            if(Isroot(p))Rotate(x);
            else if(f==ff)Rotate(p,x);
            else Rotate(x,x);
        }
    }
 
    void Access(int x){
        for(int i=0;x;i=x,x=N[x].fa){
            Splay(x);N[x][1]=i;Up(x);
        }
    }
 
    void Mkroot(int x){
        Access(x);Splay(x);N[x].fl^=1;
    }
 
    void Link(int x,int y){
        Mkroot(x);N[x].fa=y;
    }
 
    void Cut(int x,int y){
        Mkroot(x);Access(y);Splay(y);
        N[y][0]=N[x].fa=0;
    }
}
 
int main(){
    scanf("%d",&n);
    for(int i=1;i<=n+1;++i)CFA::N[i].sz=1;
    for(int i=1;i<=n;++i){
        scanf("%d",v+i);
        CFA::Link(i,min(n+1,i+v[i]));
    }
    int q;
    scanf("%d",&q);
    while(q--){
        int op,x,y;
        scanf("%d",&op);
        if(op==1){
            scanf("%d",&x);++x;
            CFA::Mkroot(n+1);CFA::Access(x);CFA::Splay(x);
            printf("%d\n",CFA::N[x].sz-1);
        }else{
            scanf("%d%d",&x,&y);++x;
            CFA::Cut(x,min(n+1,x+v[x]));
            CFA::Link(x,min(n+1,x+(v[x]=y)));
        }
    }
    return 0;
}
```

### [洞穴勘测](https://www.lydsy.com/JudgeOnline/problem.php?id=2049)

明显每次判断联不联通，没有什么困难的东西
```cpp
#include<bits/stdc++.h>
using namespace std;

#define MN 10005

namespace Tohsaka{
    struct Node{
        int fa,fl,s[2];
        int& operator[](int x){return s[x];}
    }N[MN];

    inline void Down(int x){
        if(N[x].fl){
            N[N[x][0]].fl^=1;N[N[x][1]].fl^=1;
            swap(N[x][0],N[x][1]);
            N[x].fl=0;
        }
    }

    inline bool Isroot(int x){
        return N[N[x].fa][0]!=x&&N[N[x].fa][1]!=x;
    }

    inline void Rotate(int x){
        int p=N[x].fa,pp=N[p].fa;
        int d=N[p][0]==x;
        N[N[x][d]].fa=p;
        N[p][d^1]=N[x][d];
        N[p].fa=x;N[x][d]=p;
        if(N[pp][0]==p)N[pp][0]=x;
        if(N[pp][1]==p)N[pp][1]=x;
        N[x].fa=pp;
    }

    int stk[MN],ttk=0;
    void Splay(int x){
        stk[++ttk]=x;
        for(int i=x;!Isroot(i);i=N[i].fa)stk[++ttk]=N[i].fa;
        while(ttk)Down(stk[ttk--]);
        while(!Isroot(x)){
            int p=N[x].fa,pp=N[p].fa;
            if(Isroot(p)){Rotate(x);continue;}
            int f=N[p][1]==x,ff=N[pp][1]==p;
            if(f==ff){
                Rotate(p),Rotate(x);
            }else{
                Rotate(x),Rotate(x);
            }
        }
    }

    inline void Access(int x){
        for(int i=0;x;i=x,x=N[x].fa){
            Splay(x);N[x][1]=i;
        }
    }

    inline void Mkroot(int x){
        Access(x);Splay(x);N[x].fl^=1;
    }

    inline void Link(int x,int y){
        Mkroot(x);N[x].fa=y;
    }

    inline void Cut(int x,int y){
        Mkroot(x);Access(y);Splay(y);
        N[x].fa=N[y][0]=0;
    }

    inline int Fdroot(int x){
        Access(x);Splay(x);
        while(N[x][0])x=N[x][0];
        Splay(x);
        return x;
    }
}

int main(){
    int n,m;
    scanf("%d%d",&n,&m);
    for(int i=1,x,y;i<=m;++i){
        char cc[11];
        scanf("%s%d%d",cc,&x,&y);
        if(cc[0]=='Q'){
            Tohsaka::Mkroot(x);
            if(Tohsaka::Fdroot(y)!=x)puts("No");
            else puts("Yes");
        }
        if(cc[0]=='C'){
            Tohsaka::Link(x,y);
        }
        if(cc[0]=='D'){
            Tohsaka::Cut(x,y);
        }
    }
    return 0;
}
```
