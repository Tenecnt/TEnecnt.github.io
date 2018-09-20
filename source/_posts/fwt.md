---
title: fwt学习
top: 1
mathjax: true
date: 2018-09-20 07:26:00
tags:
	- fwt
---
fwt主要是用来处理一类卷积问题的

首先说明一下集合幂级数

这就是一个用集合作为下标的函数

而fwt可以处理一类与集合幂级数相关的卷积问题

我们考虑$$\circ$$是一个关于集合的运算

现在有集合$$A,B$$

我们需要求

$$ C_{i}=\sum_{j \circ k =i} A_{j} \bigotimes B_{k} $$

其中$$\bigotimes$$是任意运算

如何处理这个问题

我们如果能构造出一种对于$$A,B$$的变换，使得

$$tf(A) \bigotimes tf(B)=tf(C)$$

(指对应位置运算，而不是卷积)

那么就可以对$$A$$和$$B$$变换，在算出$$C$$后变换回来，得到答案

那么问题在于如何得到这种变换

我们考虑最高位的情况

我们定义$$tf(A_{0})$$为最高位为$$0$$的变换结果，$$tf(A_{1})$$为最高位为$$1$$的变换结果

现在如果可以用$$tf(A_{0})$$和$$tf(A_{1})$$表示$$tf(A)$$，那问题就能解决

接下来我们对不同的运算具体分析

这里我们先定义($$A,B$$)指直接将$$A,B$$相接,后面会用到

## xor

对于xor运算，我们可以发现

$$tf(C)=(tf(A_{0})*tf(B_{0})+tf(A_{1})*tf(B_{1}),tf(A_{0})*tf(B_{1})+tf(A_{1})*tf(B_{0}))$$

这里有个结论

$$tf(A)=(tf(A_{0})+tf(A_{1}),tf(A_{0})-tf(A_{1}))$$

~~推我不会~~，但证明应该谁都会

同理逆变换

$$utf(A)=(\frac{utf(A_{0})+utf(A_{1})}{2},\frac{utf(A_{0})-utf(A_{1})}{2})$$


代码

```cpp
void txor(lg *a,int n,int on=1){
	int U=1<<n;
	for(int i=2,p=1;i<=U;i<<=1,p<<=1){
		for(int j=0;j<U;j+=i){
			for(int k=j;k<j+p;++k){
				lg A=a[k],B=a[k+p];
				a[k]=(A+B)*on%Md;a[k+p]=(A-B)*on%Md;
				if(a[k+p]<0)a[k+p]+=Md;
			}
		}
	}
}
```

## and

还是背结论吧

$$tf(A)=(tf(A_{0})+tf(A_{1}),tf(A_{1}))$$

$$utf(A)=(utf(A_{0})-utf(A_{1}),utf(A_{1}))$$

代码

```cpp
void tand(lg *a,int n,int on=1){
	int U=1<<n;
	for(int i=2,p=1;i<=U;i<<=1,p<<=1){
		for(int j=0;j<U;j+=i){
			for(int k=j;k<j+p;++k){
				a[k]=(a[k]+a[k+p]*on)%Md;
				if(a[k]<0)a[k]+=Md;
			}
		}
	}
}
```

## or

$$tf(A)=(tf(A_{0}),tf(A_{1})+tf(A_{0}))$$

$$utf(A)=(utf(A_{0}),utf(A_{1})-utf(A_{0}))$$

代码

```cpp
void tor(lg *a,int n,int on=1){
	int U=1<<n;
	for(int i=2,p=1;i<=U;i<<=1,p<<=1){
		for(int j=0;j<U;j+=i){
			for(int k=j;k<j+p;++k){
				a[k+p]=(a[k+p]+a[k]*on)%Md;
				if(a[k+p]<0)a[k+p]+=Md;
			}
		}
	}
}
```

完整代码

```cpp
namespace Ura{
    void txor(lg *a,int n,int on=1){
        int U=1<<n;
        for(int i=2,p=1;i<=U;i<<=1,p<<=1){
            for(int j=0;j<U;j+=i){
                for(int k=j;k<j+p;++k){
                    lg A=a[k],B=a[k+p];
                    a[k]=(A+B)*on%Md;a[k+p]=(A-B)*on%Md;
                    if(a[k+p]<0)a[k+p]+=Md;
                }
            }
        }
    }
    void tand(lg *a,int n,int on=1){
        int U=1<<n;
        for(int i=2,p=1;i<=U;i<<=1,p<<=1){
            for(int j=0;j<U;j+=i){
                for(int k=j;k<j+p;++k){
                    a[k]=(a[k]+a[k+p]*on)%Md;
                    if(a[k]<0)a[k]+=Md;
                }
            }
        }
    }
    void tor(lg *a,int n,int on=1){
        int U=1<<n;
        for(int i=2,p=1;i<=U;i<<=1,p<<=1){
            for(int j=0;j<U;j+=i){
                for(int k=j;k<j+p;++k){
                    a[k+p]=(a[k+p]+a[k]*on)%Md;
                    if(a[k+p]<0)a[k+p]+=Md;
                }
            }
        }
    }
}
```
