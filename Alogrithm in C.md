# Data Structure and Algorithms 

B.W.	Sc44964

[TOC]

## [Introduction]

 	Data Structures are the programmatic way of storing data so that data can be used efficiently. Almost every' enterprise application uses various types of data structures in one or the other way.

​	Algorithm is a step-by-step procedure, which defines a set of instructions to be executed in a certain order to get the desired output. Algorithms are generally created independent of underlying languages, i.e. an algorithm can be implemented in more than one programming language.

​	Here in the club,we need to learn by ourself .Though I have found it's hard,difficult,tough......But nothing of great significance is easy to get .All we need to do is to  put all our will against here ,and it's amazing what can be achieved . 

​     So let's start the hard way .

​    Please remember,

​    所谓无底深渊，下去也是前程万里。

​                                                                                           B.W.

​                                                                                         2017.4.1

[Reference]:*Data Structures and Algorithm Analysis in C*

[Website Resources]:

https://www.tutorialspoint.com/data_structures_algorithms/data_structure_overview.htm

https://www.hackerrank.com/dashboard

***

## 第一部分 基础知识

### 第1章 引言

对于连通性问题(*Dynamic Connectivity*)，书中首先提出的是快速查找算法(*quick-find algorithm*)。大意是把连通的id值赋值为同一个，用id值的等价来表示它的连通。

过程大致如下：

1. 初始化数组
2. 获取输入
   - 如果输入的两个值的id相等，则跳出（已连通）
   - 否则，就把所有id值为id[p]的元素变为id[q]，相同的id表示它们在同一个集合（标志连通属性）并在下一次循环中输出上一次的结果（未连通则输出p-q，连通则不输出）

#### 快速查找算法

```c
#include <stdio.h>
#define N 10000
int main(){
  int i, p, q, t, id[N];
  // 初始化数组
  for(i = 0; i < N; i++)
    id[i] = i;
  while(scanf(" %d %d\n", &p, &q) == 2){
    // 如果输入的两个值得id相等，则跳出（已连通）
    if(id[p] == id[q])
    {
         printf(" %d %d之前已连通\n", p, q);
         continue;
    }

    // 检查所有id值为id[p]的元素，并将其值置为id[q]
    for(t = id[p], i = 0; i < N; i++)
      if(id[i] == t)
        id[i] = id[q];
    printf(" %d %d之前未连通\n", p, q);
  }
  return 0;
}
```

优点：查找快速

缺点：慢速合并，对于每个合并操作，for循环迭代N次，N个对象，至少执行MN次，在大量的对象和输入时不可取。

***

但也可按照根(*root*)查找，根一定是指向自身的，每一次输入，都是“枝”的生长、合并，当查找的时候，找的是位于“树根”（枝的尽头）处的值，只要“树根”处的值位于一个集合中，则它们在一个“树”上，那我们就可以说，它们是连通的，这就是快速合并算法。

那么此时的数组就比第一种更加抽象了，数组的id值其实是该节点的“上一个值”，即父节点，通过数组的不停迭代上溯查找，来找到“树根”。

则有代码：

#### 快速合并算法

```c
#include <stdio.h>
#define N 10000
int main(){
  int i, j, p, q, t, id[N];
  // 初始化数组,每一个元素都是自己的“根”
  for(i = 0; i < N; i++)
    id[i] = i;
  while(scanf(" %d %d\n", &p, &q) == 2){
    // 两个for循环都是为了找到各自的“根”。如果i和id值相等，那么
    // 自然有i == id[i]，即找到了“根”，退出循环；如果i和id值不
    // 等，则此值一定会通过“上溯”的方法找到“根”的位置，即，把其id
    // 值作为新的查找值，直至找出其“根”。
    for(i = p; i != id[i]; i = id[i])
      ;
    for(j = q; j != id[j]; j = id[j])
      ;
    // 如果“根”相同，则说明它们在同一个集合中；否则，就将其归入
    // 另外一个集合中。
    if(i == j){
      printf(" %d %d之前已连通\n", p, q);
      continue;
    }
    id[i] = j;
    printf(" %d %d之前未连通\n", p, q);
  }
  return 0;
}
```

***

但是考察一下第二个算法，当进行树的合并(*id[i] = j*)的时候，如果把较大的树合并到较小的树上，就会造成查找的耗时。而我们可以通过增加一个判断，使较小的树总是连接到较大的树上，从而达到优化算法的目的。

这样的话，就需要一个size数组来记录每一个树的大小，在连接时进行比较，由此，将较小的树连接到较大的树上。

则有代码：

#### 加权快速合并算法

```c
#include <stdio.h>
#define N 10000
int main(){
  int i, j, p, q, t, id[N], sz[N];
  for(i = 0; i < N; i++){
    id[i] = i;
    sz[i] = 1; // 每一个独立单元都是一个size为1的树
  }
  while(scanf(" %d %d\n", &p, &q) == 2){
    for(i = p; i != id[i]; i = id[i])
      ;
    for(j = q; j != id[j]; j = id[j])
      ;
    if(i == j){
      printf(" %d %d之前已连通\n", p, q);
      continue;
    }
    // 加权合并
    if(sz[i] < sz[j]){
      id[i] = j;
      sz[j] += sz[i];
    }else{
      id[j] = i;
      sz[i] += sz[j];
    }
    printf(" %d %d之前未连通\n", p, q);
  }
  return 0;
}
```

优点：树中每个节点到根节点的距离变小，因而查找效率更高，非常高。

***

但对于这个算法而言，我们可以把一个树进行路径压缩，使树“平扁化”。

#### 等分路径压缩算法

```c
#include <stdio.h>
#define N 10000
int main(){
  int i, j, p, q, t, id[N], sz[N];
  for(i = 0; i < N; i++){
    id[i] = i;
    sz[i] = 1; // 每一个独立单元都是一个size为1的树
  }
  while(scanf(" %d %d\n", &p, &q) == 2){
    for(i = p; i != id[i]; i = id[i])
      id[i] = id[id[i]];
    for(j = q; j != id[j]; j = id[j])
      id[j] = id[id[j]];
    if(i == j){
      printf(" %d %d之前已连通\n", p, q);
      continue;
    }
    // 加权合并
    if(sz[i] < sz[j]){
      id[i] = j;
      sz[j] += sz[i];
    }else{
      id[j] = i;
      sz[i] += sz[j];
    }
    printf(" %d %d之前未连通\n", p, q);
  }
  return 0;
}
```
***

#### Conclusion

第一章通-过对连通性问题求解的逐步求精给我们展示了一个算法从雏形到完善的整个过程，这一章讲算法的目的并不在于解决这一问题，而在于展现出设计算法的基础步骤：

1. 确定完整、明确的问题描述，包括确定问题固有的基本抽象操作。
2. 仔细设计一个简单算法的简明实现。
3. 通过逐步求精的过程开发改进后的实现，经过实验分析、数学分析或两者共同验证改进思想的效率。
4. 找出数据结构或者算法操作的高级抽象表示，能够使改进版本的设计性能高效。
5. 可能时尽量保证最坏情况下的性能，但实际数据可用时接受好的性能。

而对于第一章的学习，我们可以知道数组的抽象形式。一个整型数组不仅仅只是内存中占有等长字节的连续的一组值，它可通过位-值得关系来进行排序（桶排序），也可以抽象成集合、树（如上），甚至还可以对树进行加权、压缩！

And......................

Not all procedures can be called an algorithm. An algorithm should have the following characteristics −

- **Unambiguous** − Algorithm should be clear and unambiguous. Each of its steps (or phases), and their inputs/outputs should be clear and must lead to only one meaning.
- **Input** − An algorithm should have 0 or more well-defined inputs.
- **Output** − An algorithm should have 1 or more well-defined outputs, and should match the desired output.
- **Finiteness** − Algorithms must terminate after a finite number of steps.
- **Feasibility** − Should be feasible with the available resources.
- **Independent** − An algorithm should have step-by-step directions, which should be independent of any programming code.

所以我们下面再开始分析算法吧。。。。（前言还有一些超级变态的课后题，一起来做做吧。。。）

### 第2章 算法分析的原理

#### PRE READINGS

Algorithm is a step-by-step procedure, which defines a set of instructions to be executed in a certain order to get the desired output. Algorithms are generally created independent of underlying languages, i.e. an algorithm can be implemented in more than one programming language.

From the data structure point of view, following are some important categories of algorithms −

- **Search** − Algorithm to search an item in a data structure.
- **Sort** − Algorithm to sort items in a certain order.
- **Insert** − Algorithm to insert item in a data structure.
- **Update** − Algorithm to update an existing item in a data structure.
- **Delete** − Algorithm to delete an existing item from a data structure.

##### Characteristics of an Algorithm

Not all procedures can be called an algorithm. An algorithm should have the following characteristics −

- **Unambiguous** − Algorithm should be clear and unambiguous. Each of its steps (or phases), and their inputs/outputs should be clear and must lead to only one meaning.
- **Input** − An algorithm should have 0 or more well-defined inputs.
- **Output** − An algorithm should have 1 or more well-defined outputs, and should match the desired output.
- **Finiteness** − Algorithms must terminate after a finite number of steps.
- **Feasibility** − Should be feasible with the available resources.
- **Independent** − An algorithm should have step-by-step directions, which should be independent of any programming code.

##### How to Write an Algorithm?

There are no well-defined standards for writing algorithms. Rather, it is problem and resource dependent. Algorithms are never written to support a particular programming code.

As we know that all programming languages share basic code constructs like loops (do, for, while), flow-control (if-else), etc. These common constructs can be used to write an algorithm.

We write algorithms in a step-by-step manner, but it is not always the case. Algorithm writing is a process and is executed after the problem domain is well-defined. That is, we should know the problem domain, for which we are designing a solution.

##### Example

Let's try to learn algorithm-writing by using an example.

**Problem** − Design an algorithm to add two numbers and display the result.

```
step 1 − START
step 2 − declare three integers a, b & c
step 3 − define values of a & b
step 4 − add values of a & b
step 5 − store output of step 4 to c
step 6 − print c
step 7 − STOP
```

Algorithms tell the programmers how to code the program. Alternatively, the algorithm can be written as −

```
step 1 − START ADD
step 2 − get values of a & b
step 3 − c ← a + b
step 4 − display c
step 5 − STOP
```

In design and analysis of algorithms, usually the second method is used to describe an algorithm. It makes it easy for the analyst to analyze the algorithm ignoring all unwanted definitions. He can observe what operations are being used and how the process is flowing.

Writing **step numbers**, is optional.

We design an algorithm to get a solution of a given problem. A problem can be solved in more than one ways.

Hence, many solution algorithms can be derived for a given problem. The next step is to analyze those proposed solution algorithms and implement the best suitable solution.

##### Algorithm Analysis

Efficiency of an algorithm can be analyzed at two different stages, before implementation and after implementation. They are the following −

- **A Priori Analysis** − This is a theoretical analysis of an algorithm. Efficiency of an algorithm is measured by assuming that all other factors, for example, processor speed, are constant and have no effect on the implementation.
- **A Posterior Analysis** − This is an empirical analysis of an algorithm. The selected algorithm is implemented using programming language. This is then executed on target computer machine. In this analysis, actual statistics like running time and space required, are collected.

We shall learn about a priori algorithm analysis. Algorithm analysis deals with the execution or running time of various operations involved. The running time of an operation can be defined as the number of computer instructions executed per operation.

##### Algorithm Complexity

Suppose **X** is an algorithm and **n** is the size of input data, the time and space used by the algorithm X are the two main factors, which decide the efficiency of X.

- **Time Factor** − Time is measured by counting the number of key operations such as comparisons in the sorting algorithm.
- **Space Factor** − Space is measured by counting the maximum memory space required by the algorithm.

The complexity of an algorithm **f(n)** gives the running time and/or the storage space required by the algorithm in terms of **n** as the size of input data.

##### Space Complexity

Space complexity of an algorithm represents the amount of memory space required by the algorithm in its life cycle. The space required by an algorithm is equal to the sum of the following two components −

- A fixed part that is a space required to store certain data and variables, that are independent of the size of the problem. For example, simple variables and constants used, program size, etc.
- A variable part is a space required by variables, whose size depends on the size of the problem. For example, dynamic memory allocation, recursion stack space, etc.

Space complexity S(P) of any algorithm P is S(P) = C + SP(I), where C is the fixed part and S(I) is the variable part of the algorithm, which depends on instance characteristic I. Following is a simple example that tries to explain the concept −

```
Algorithm: SUM(A, B)
Step 1 -  START
Step 2 -  C ← A + B + 10
Step 3 -  Stop
```

Here we have three variables A, B, and C and one constant. Hence S(P) = 1 + 3. Now, space depends on data types of given variables and constant types and it will be multiplied accordingly.

##### Time Complexity

Time complexity of an algorithm represents the amount of time required by the algorithm to run to completion. Time requirements can be defined as a numerical function T(n), where T(n) can be measured as the number of steps, provided each step consumes constant time.

For example, addition of two n-bit integers takes **n** steps. Consequently, the total computational time is T(n) = c ∗ n, where c is the time taken for the addition of two bits. Here, we observe that T(n) grows linearly as the input size increases.

Asymptotic analysis of an algorithm refers to defining the mathematical boundation/framing of its run-time performance. Using asymptotic analysis, we can very well conclude the best case, average case, and worst case scenario of an algorithm.

Asymptotic analysis is input bound i.e., if there's no input to the algorithm, it is concluded to work in a constant time. Other than the "input" all other factors are considered constant.

Asymptotic analysis refers to computing the running time of any operation in mathematical units of computation. For example, the running time of one operation is computed as *f*(n) and may be for another operation it is computed as *g*(n2). This means the first operation running time will increase linearly with the increase in **n** and the running time of the second operation will increase exponentially when **n** increases. Similarly, the running time of both operations will be nearly the same if **n** is significantly small.

Usually, the time required by an algorithm falls under three types −

- **Best Case** − Minimum time required for program execution.
- **Average Case** − Average time required for program execution.
- **Worst Case** − Maximum time required for program execution.

##### Asymptotic Notations

Following are the commonly used asymptotic notations to calculate the running time complexity of an algorithm.

- $\mathcal{O}$ Notation
- $\varOmega$ Notation
- $\varTheta$ Notation

##### Big Oh Notation 

![Big O Notation](https://www.tutorialspoint.com/data_structures_algorithms/images/big_o_notation.jpg)

The notation $\mathcal{O}$ (n) is the formal way to express the upper bound of an algorithm's running time. It measures the worst case time complexity or the longest amount of time an algorithm can possibly take to complete.

For example, for a function **f(n)**

$$
\mathcal{O}(f(n))=\big\{g(n):\textrm{ there exists }c\gt0\textrm{ and }n_0\textrm{ such that }f(n)\lt cg(n)\textrm{ for all }n\gt n_0 \big\}
$$



##### Omega Notation

![Omega Notation](https://www.tutorialspoint.com/data_structures_algorithms/images/omega_notation.jpg)

The notation  $\varOmega$(n) is the formal way to express the lower bound of an algorithm's running time. It measures the best case time complexity or the best amount of time an algorithm can possibly take to complete.

For example, for a function **f(n)**
$$
\varOmega(f(n))\ge\big\{g(n):\textrm{ there exists }c\gt0\textrm{ and }n_0\textrm{ such that }g(n)\le cf(n)\textrm{ for all }n\gt n_0\big\}
$$

##### Theta Notation

![Theta Notation](https://www.tutorialspoint.com/data_structures_algorithms/images/theta_notation.jpg)

The notation θ(n) is the formal way to express both the lower bound and the upper bound of an algorithm's running time. It is represented as follows,
$$
\varTheta(f(n))=\big\{g(n)\textrm{ if and only if } g(n)=\mathcal{O}(f(n))\textrm{ and }g(n)=\varOmega(f(n))\textrm{ for all }n\gt n_0.\big\}
$$

##### Common Asymptotic Notations

Following is a list of some common asymptotic notations −

| constant    |    − | $\mathcal{O}(1)$        |
| ----------- | ---: | ----------------------- |
| logarithmic |    − | $\mathcal{O}(\log n)$   |
| linear      |    − | $\mathcal{O}(n)$        |
| n log n     |    − | $\mathcal{O}(n \log n)$ |
| quadratic   |    − | $\mathcal{O}(n^2)$      |
| cubic       |    − | $\mathcal{O}(n^3)$      |
| polynomial  |    − | $n^{\mathcal{O}(1)}$    |
| exponential |    − | $2^{\mathcal{O}(n)}$    |

鉴于这部分内容基础部分大部分可以自行理解，觉得https://www.tutorialspoint.com/data_structures_algorithms/asymptotic_analysis.htm的Data Structures - Asymptotic Analysis部分梳理的很好,整理在这里，阅读一遍即可。

#### Body

$\mathcal{O}$(*O-notation*)：【渐进上界】如果存在常数$c_0$和$N_0$，对于所有$N>N_0$，有$g(N)<c_0f(N)$,则称函数$g(N)$是$\mathcal{O}(f(N))$的。

- 限制忽略数学公式中的低阶项时产生的误差。
- 限制由于忽略对程序的总运行时间贡献较小的某些部分时产生的错误。
- 允许我们按照算法总运行时间的上界对算法进行分类。

基本递归方程

- 程序的循环通过输入每次减少一项
  $$
  C_N=C_{N-1}+N, N\ge 2,C_1=1\\
  C_N=\frac{N(N+1)}{2}
  $$

- 程序每次使输入减半

- $$
  C_N=C_{N/2}+1, N\ge2,C_1=1\\
  C_{2^n}=n+1
  $$

- 程序每次使输入减半，但需检查输入的每一项

- $$
  C_N=C_{N/2}+N,N\ge2,C_1=0\\
  C_N\thickapprox2N
  $$

- 程序把输入分成两半，但在划分之前、划分之中以及划分之后需要线性遍历输入

- $$
  C_N=2C_{N/2}+N,N\ge2,C_1=0\\
  C_{2^n}=n2^n
  $$






#####  Extend Readings

[Time complexity](https://en.wikipedia.org/wiki/Time_complexity)

[Big O nonation](https://en.wikipedia.org/wiki/Big_O_notation)

[A beginner's guide to Big O notation](https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/)

## 第二部分 数据结构

### 第3章 基本数据结构

#### Basic Concepts

[Tutorial Point](https://www.tutorialspoint.com/data_structures_algorithms/data_structures_basics.htm)

This chapter explains the basic terms related to data structure.

##### Data Definition

Data Definition defines a particular data with the following characteristics.

- **Atomic** − Definition should define a single concept.
- **Traceable** − Definition should be able to be mapped to some data element.
- **Accurate** − Definition should be unambiguous.
- **Clear and Concise** − Definition should be understandable.

##### Data Object

Data Object represents an object having a data.

##### Data Type

Data type is a way to classify various types of data such as integer, string, etc. which determines the values that can be used with the corresponding type of data, the type of operations that can be performed on the corresponding type of data. There are two data types −

- Built-in Data Type
- Derived Data Type

##### Built-in Data Type

Those data types for which a language has built-in support are known as Built-in Data types. For example, most of the languages provide the following built-in data types.

- Integers
- Boolean (true, false)
- Floating (Decimal numbers)
- Character and Strings

##### Derived Data Type

Those data types which are implementation independent as they can be implemented in one or the other way are known as derived data types. These data types are normally built by the combination of primary or built-in data types and associated operations on them. For example −

- List
- Array
- Stack
- Queue

##### Basic Operations

The data in the data structures are processed by certain operations. The particular data structure chosen largely depends on the frequency of the operation that needs to be performed on the data structure.

- Traversing
- Searching
- Insertion
- Deletion
- Sorting
- Merging

***

#### Body

在计算机上处理的所有数据最终都分解到单个位。**类型**允许我们指定如何利用特定位的各种集合，**函数**允许我们指定在数据上所进行的操作。

**数据类型**是值的集合和在这些值上的操作集，是数值集合（内置类型或其他类型）及其相关操作（函数）的一个列表。

我们可以把一个程序组织为三个文件【推荐】

- ***Interface*** 定义了数据结构以及声明用于操作这个数据结构的函数
- ***Implementation*** 在接口中声明函数的一个实现
- ***Client*** 调用接口中声明的函数，以便在更高抽象层次上使用

一个具体实现的例子如下（计算随机数的平均值及方差）：

- ***Interface***

  "Num.h"

  ```c
  typedef int Number;
  Number randNum();
  ```

- ***Implementation***

  "int.c"

  ```c
  #include <stdio.h>
  #include "Num.h"
  Number randNum(){
    return rand();
  }
  ```

- ***Client***

  "avg.c"

  ```c
  #include <stdio.h>
  #include <math.h>
  #include "Num.h"
  int main(int argc, char *argv[]){
    int i, N = atoi(argv[1]);
    float m1 = 0.0, m2 = 0.0;
    Number x;
    for(i = 0; i < N; i++){
      x = randNum();
      m1 += ((float) x) / N;
      m2 += ((float) x*x) / N;
    }
    printf("       Average: %lf\n", m1);
    printf("Std.devitation: %lf\n", sqrt(m2-m1*m1));
    return 0;
  }
  ```

##### Array

###### （1）埃拉托色尼算法

功能：如果自然数为素数，a[i]=1,否则a[i]=0。

- 把数组中所有元素设为1，以表明没有任何数被证明为非素数
- 把数组中所有对应索引处已证明是非素数（已知素数的倍数）的元素设为0.如果所有更小的倍数都已经设为0，a[i]仍然为1，则可知它是素数。

```c
#include<stdio.h>
#define N 10000
int main ()
{
    int i,j,a[N];
    for(i=2;i<N;i++)
        a[i]=1;//把数组中所有元素设为1，以表明没有任何数被证明为非素数
    for(i=2;i<N;i++)
        if(a[i])
          for(j=i;j*i<N;j++)a[j*i]=0;//所有小于10000，某数的倍数，都不是素数。
    for(i=2;i<N;i++)
        if(a[i])printf("%8d",i);
    return 0;
}
```

###### （2）数组的动态存储分配——抛硬币模拟试验（Bernoulli trial)

​      上面 （1）中的最大数N为已知，而若要改变N，只能修改程序重新编译。另一种方法是：程序从命令行中获取期望的最大数，再通过stdilb.c的malloc，利用该值为数组动态分配内存.

下面举一个实例：伯努利实验：如果我们抛一枚硬币N次，那么看到k次正面的概率是
$$
\heartsuit\heartsuit\heartsuit{N \choose k} \frac{1}{2^N}\thickapprox \frac{e^{-(k- N/2)^2/N} }{\sqrt{ \pi N/2} }\heartsuit\heartsuit\heartsuit
$$
[$LaTeX{}$](http://mohu.org/info/symbols/symbols.htm)

```c
#include<stdio.h>
#include<stdlib.h>
int heads()
{
    return rand() <RAND_MAX/2;//小于返回1，不小于返回0，概率1/2
}
int main()
{
    int M,N;
    int i ,j,cnt=0;
    scanf("%d",&M);//输入抛硬币的次数N和实验的次数M
    scanf("%d",&N);
    int *f=malloc((N+1)*sizeof(int));//动态申请内存储存存储数据
    for(j=0;j<=N;j++)
        f[j]=0;
    for(i=0;i<M;i++,f[cnt]++)
        for(cnt=0,j=0;j<N;j++) //类似于桶排序
           if(heads()) cnt++;
    for(j=0;j<=N;j++)
    {
        printf("%2d",j);
        for(i=0;i<f[j];i+=10) printf("*");//以10为单位，输出出现j次的次数
        printf("\n");
    }
    return 0;

}
```

当M=8000，N=32的时候，结果非常满足动态分布

![伯努利实验](C:\Users\Scorpio\Documents\Tencent Files\3302212953\FileRecv\伯努利实验.png)

###### （3）最近点对的计算

对N个随机产生的单位正方形中的点，统计可以被长度小于d的直线连结的点的对数。

```c
#include<stdio.h>
#include<stdlib.h>
#include<math.h>
typedef struct
{
    float x;
    float y;
}Point;//定义一个结构体存放点的横纵坐标
float randFloat()
{
    return 1.0*rand()/RAND_MAX;随机产生一个[0,1]的浮点数
}
float distance( Point a, Point b)
{
    float dx = a.x-b.x,dy=a.y-b.y;
    return sqrt(dx*dx+dy*dy);
}//计算两点之间的距离
int main()
{
    float d;
    int N;
    int i ,j,cnt=0;
    scanf("%f",&d);//读入距离
    scanf("%d",&N);//点的个数
    Point *a=malloc(N*sizeof(*a));//结构体数组
    for(i=0;i<N;i++)
    {
        a[i].x=randFloat();
        a[i].y=randFloat();
    }
    for(i=0;i<N;i++)
    {
        for(j=i+1;j<N;j++)
        {
            if(distance(a[i],a[j])<d) cnt++;
        }
    }
    printf("%d edges shorter than %f\n",cnt,d);
    return 0;

}
```

虽然枚举很暴力，但是我们这里主要学了创建任意复杂类型的方法：可以是结构体数组，可以是数组组成的数组，或者包含数组的结构体。之后我们会仔细考虑各种数组的用法，但是下一章，还是不得不来链表了啊。。。表示我看了好多本关于数据结构的书都只能看到链表。。。。。但是这是除数组外另一种组织对象集合的主要方法。

##### Linked List

**链表**是一组数据项的集合，其中每个数据项都是一个节点的一部分，每个节点都包含指向下一个节点的链接。

**链表**是*自引用*结构（通过引用节点来定义节点（结构体的自引用））；*循环*结构，节点的链接可指向自身。

对于**链表**中的末尾节点有如下约定：

- 将其置为不指向任何节点的空链接(NULL)
- 使其指向一个不包含元素节点的哑元节点（不表示实际数据的节点）
- 使其指向第一个节点（首节点），使其成为循环链表



#### Extend Readings

##### C Fundamentals

[*The C Programming Language*](https://www.amazon.com/Programming-Language-Brian-W-Kernighan/dp/0131103628)

[*C Language in Stack Overflow*](https://stackoverflow.com/documentation/c/topics)

[Stanford CS Library](http://cslibrary.stanford.edu/)

***

​											LAST UPDATE ON 4.2.2017