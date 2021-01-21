# 基础

### 状态设计

如果理解了上述过程，我们需要考虑的就是怎样判断现在在哪一层，怎样判断当前的状态——这就需要我们传进一些参量。

**dfs函数需要哪些参量?**

- 首先是数位dp基本的量数字位数 pos ，记录答案的 st ，最高位限制 limit （这个后面会讲）
- 我们还需要一个判断判断前导0的标记 lead （这个后面也会讲）
- 由于数位dp解决的大多是数字组成问题，所以经常要比较当前位和前一位或前几位的关系（根据题意而定），所以一般在dfs()中也要记录前一位或前几位数 pre 方便比较。
- 除此之外还可以传进更多参量以区分状态，视题意而定。

数位dp的状态能记录的最好都记录上 ——lwz dalao

**【细节分析】**

**一、前导0标记lead**

由于我们要搜的数可能很长，所以我们的直接最高位搜起

举个例子：假如我们要从 [0,1000] 找任意相邻两数相等的数

显然 111,222,888 等等是符合题意的数

但是我们发现右端点 1000 是四位数

因此我们搜索的起点是 0000 ，而三位数的记录都是 0111,0222,0888 等等

而这种情况下如果我们直接找相邻位相等则 0000 符合题意而 0111,0222,0888 都不符合题意了

所以我们要加一个前导0标记

- 如果当前位 lead=1 而且当前位也是0，那么当前位也是前导0， pos+1 继续搜；
- 如果当前位 lead=1 但当前位不是0，则本位作为当前数的最高位， pos+1 继续搜；（注意这次根据题意st或其他参数可能发生变化）

当然前导 0 有时候是不需要判断的，上述的例子是一个有关数字结构上的性质，0会影响数字的结构，所以必须判断前导0；而如果我们研究的是数字的组成（例如这个数字有多少个 1 之类的问题），0并不影响我们的判断，这样就不需要前导0标记了。总之，这个因题而异，并不是必须要标记（当然记了肯定是不会出错的）

### 最高位标记limit

我们知道在搜索的数位搜索范围可能发生变化；

举个例子：我们在搜索 [0,555] 的数时，显然最高位搜索范围是 0 ~ 5 ，而后面的位数的取值范围会根据上一位发生变化：

- 当最高位是 1 ~ 4 时，第二位取值为 [0,9] ;
- 当最高位是 5 时，第二位取值为 [0,5] （再往上取就超出右端点范围了）

为了分清这两种情况，我们引入了 \text{limit} 标记：

- 若当前位 limit=1 而且已经取到了能取到的最高位时，下一位 limit=1 ；
- 若当前位 limit=1 但是没有取到能取到的最高位时，下一位 limit=0 ；
- 若当前位 limit=0 时，下一位 limit=0 。

我们设这一位的标记为 limit ，这一位能取到的最大值为 res ，则下一位的标记就是 i==res && limit （ i 枚举这一位填的数）

### dp值的记录和使用

最后我们考虑dp数组下标记录的值

本文介绍数位dp是在记忆化搜索的框架下进行的，每当找到一种情况我们就可以这种情况记录下来，等到搜到后面遇到相同的情况时直接使用当前记录的值。

dp数组的下标表示的是一种状态，只要当前的状态和之前搜过的某个状态完全一样，我们就可以直接返回原来已经记录下来的dp值。

再举个例子

假如我们找 [0,123456] 中符合某些条件的数

假如当我们搜到 1000jQuery33108684357930728313_1541404441669 时，dfs从下返上来的数值就是当前位是第 5 位，前一位是 0 时的方案种数，搜完这位会向上反，这是我们可以记录一下：当前位第 5 位，前一位是 0 时，有这么多种方案种数

当我们继续搜到 1010?? 时，我们发现当前状态又是搜到了第 5 位，并且上一位也是 0 ，这与我们之前记录的情况相同，这样我们就可以不继续向下搜，直接把上次的dp值返回就行了。

注意，我们返回的dp值必须和当前处于完全一样的状态，这就是为什么dp数组下标要记录 pos,pre 等参量了。

最重要的来了——

接着上面的例子，范围 [0,123456]

如果我们搜到了 1234?? ，我们能不能直接返回之前记录的：当前第 5 位，前一位是 4 时的dp值？

答案是否定的

我们发现，这个状态的dp值被记录时，当前位也就是第 5 位的取值是 [0,9] ，而这次当前位的取值是 [0,5] ，方案数一定比之前记录的dp值要小。

当前位的取值范围为什么会和原来不一样呢？

如果你联想到了之前所讲的知识，你会发现：现在的 limit=1 ，最高位有取值的限制。

因此我们可以得到一个结论：当 limit=1 时，不能记录和取用dp值！

类似上述的分析过程，我们也可以得出：当 lead=1 时，也不能记录和取用dp值！

p.s.当然没有这么绝对的说……因题而异的说……

以上就是计划搜索的完整步骤。

附图：

![img](https://5b0988e595225.cdn.sohucs.com/images/20181106/7a2ad91e0761427a81308da7876519fc.png)

# 实战

### 模板

```cpp
typedef long long ll;
int a[20];
ll dp[20][state];//不同题目状态不同
ll dfs(int pos,/*state变量*/,bool lead/*前导零*/,bool limit/*数位上界变量*/)//不是每个题都要判断前导零
{
    //递归边界，既然是按位枚举，最低位是0，那么pos==-1说明这个数我枚举完了
    if(pos==-1) return 1;/*这里一般返回1，表示你枚举的这个数是合法的，那么这里就需要你在枚举时必须每一位都要满足题目条件，也就是说当前枚举到pos位，一定要保证前面已经枚举的数位是合法的。不过具体题目不同或者写法不同的话不一定要返回1 */
    //第二个就是记忆化(在此前可能不同题目还能有一些剪枝)
    if(!limit && !lead && dp[pos][state]!=-1) return dp[pos][state];
    /*常规写法都是在没有限制的条件记忆化，这里与下面记录状态是对应，具体为什么是有条件的记忆化后面会讲*/
    int up=limit?a[pos]:9;//根据limit判断枚举的上界up;
    ll ans=0;
    //开始计数
    for(int i=0;i<=up;i++)//枚举，然后把不同情况的个数加到ans就可以了
    {
        if() ...
        else if()...
        ans+=dfs(pos-1,/*状态转移*/,lead && i==0,limit && i==a[pos]) //最后两个变量传参都是这样写的
        /*这里还算比较灵活，不过做几个题就觉得这里也是套路了
        大概就是说，我当前数位枚举的数是i，然后根据题目的约束条件分类讨论
        去计算不同情况下的个数，还有要根据state变量来保证i的合法性，比如题目
        要求数位上不能有62连续出现,那么就是state就是要保存前一位pre,然后分类，
        前一位如果是6那么这意味就不能是2，这里一定要保存枚举的这个数是合法*/
    }
    //计算完，记录状态
    if(!limit && !lead) dp[pos][state]=ans;
    /*这里对应上面的记忆化，在一定条件下时记录，保证一致性，当然如果约束条件不需要考虑lead，这里就是lead就完全不用考虑了*/
    return ans;
}
ll solve(ll x)
{
    int pos=0;
    while(x)//把数位都分解出来
    {
        a[pos++]=x%10;//个人老是喜欢编号为[0,pos),看不惯的就按自己习惯来，反正注意数位边界就行
        x/=10;
    }
    return dfs(pos-1/*从最高位开始枚举*/,/*一系列状态 */,true,true);//刚开始最高位都是有限制并且有前导零的，显然比最高位还要高的一位视为0嘛
}
int main()
{
    ll le,ri;
    while(~scanf("%lld%lld",&le,&ri))
    {
        //初始化dp数组为-1,这里还有更加优美的优化,后面讲
        printf("%lld\n",solve(ri)-solve(le-1));
    }
}
```

### 实战篇

https://blog.csdn.net/jk211766/article/details/81474632

> 例一：[HDU 2089](http://acm.hdu.edu.cn/showproblem.php?pid=2089) 不要62
>
> 入门题。就是数位上不能有4也不能有连续的62，没有4的话在枚举的时候判断一下，不枚举4就可以保证状态合法了，所以这个约束没有记忆化的必要，而对于62的话，涉及到两位，当前一位是6或者不是6这两种不同情况我计数是不相同的，所以要用状态来记录不同的方案数。
>
> dp[pos] [sta]表示当前第pos位，前一位是否是6的状态，这里sta只需要去0和1两种状态就可以了，不是6的情况可视为同种，不会影响计数。

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<string>
using namespace std;
typedef long long ll;
int a[20];
int dp[20][2];
int dfs(int pos,int pre,int sta,bool limit)
{
    if(pos==-1) return 1;
    if(!limit && dp[pos][sta]!=-1) return dp[pos][sta];
    int up=limit ? a[pos] : 9;
    int tmp=0;
    for(int i=0;i<=up;i++)
    {
        if(pre==6 && i==2)continue;
        if(i==4) continue;//都是保证枚举合法性
        tmp+=dfs(pos-1,i,i==6,limit && i==upper);
    }
    if(!limit) dp[pos][sta]=tmp;
    return tmp;
}
int solve(int x)
{
    int pos=0;
    while(x)
    {
        a[pos++]=x%10;
        x/=10;
    }
    return dfs(pos-1,-1,0,true);
}
int main()
{
    int le,ri;
    //memset(dp,-1,sizeof dp);可优化
    while(~scanf("%d%d",&le,&ri) && le+ri)
    {
        memset(dp,-1,sizeof dp);
        printf("%d\n",solve(ri)-solve(le-1));
    }
    return 0;
}
```

 

入门就不多讲了，开始讲常用优化吧！



这一点是一个数位特点，使用的条件是：约束条件是每个数自身的属性，而与输入无关。

具体的：上一题不要62和4，这个约束对每一个数都是确定的，就是说任意一个数满不满足这个约束都是确定，比如444这个数，它不满足约束条件，不管你输入的区间是多少你都无法改变这个数不满足约束这个事实，这就是数自身的属性（我们每组数据只是在区间计数而已，只能说你输入的区间不包含444的话，我们就不把它统计在内，而无法改变任何事实）。

由此，我们保存的状态就可以一直用(注意还有要limit，不同区间是会影响数位在有限制条件下的上限的)

这点优化就不给具体题目了，这个还有进一步的扩展。不过说几个我遇到的简单的约束：

1.求数位和是10的倍数的个数,这里简化为数位sum%10这个状态，即dp[pos][sum]这里10 是与多组无关的，所以可以memset优化，不过注意如果题目的模是输入的话那就不能这样了。

2.求二进制1的数量与0的数量相等的个数，这个也是数自身的属性。

3.。。。。。

还是做题积累吧。搞懂思想！

下面介绍的方法就是要行memset优化，把不满足前提的通过修改，然后优化。

介绍之前,先说一种较为笨拙的修改，那就是增加状态，前面讲limit的地方说增加一维dp[pos][state][limit]，能把不同情况下状态分别记录(不过这个不能memset放外面)。基于这个思想，我们考虑：约束为数位是p的倍数的个数，其中p数输入的，这和上面sum%10类似，但是dp[pos][sum]显然已经不行了，每次p可能都不一样，为了强行把memset提到外面加状态dp[pos][sum][p]，对于每个不同p分别保存对应的状态。这里前提就比较简单了，你dp数组必须合法，p太大就G_G了。所以对于与输入有关的约束都可以强行增加状态(这并不代表能ac，如果题目数据少的话就随便你乱搞了)

### 第二：相减。

例题：[HDU 4734](http://acm.hdu.edu.cn/showproblem.php?pid=4734)

题目给了个f(x)的定义：F(x) = An * 2n-1 + An-1 * 2n-2 + ... + A2 * 2 + A1 * 1，Ai是十进制数位，然后给出a,b求区间[0,b]内满足f(i)<=f(a)的i的个数。

常规想：这个f(x)计算就和数位计算是一样的，就是加了权值，所以dp[pos][sum]，这状态是基本的。a是题目给定的，f(a)是变化的不过f(a)最大好像是4600的样子。如果要memset优化就要加一维存f(a)的不同取值，那就是dp[10][4600][4600]，这显然不合法。

这个时候就要用减法了，dp[pos][sum]，sum不是存当前枚举的数的前缀和(加权的)，而是枚举到当前pos位，后面还需要凑sum的权值和的个数，

也就是说初始的是时候sum是f(a),枚举一位就减去这一位在计算f(i)的权值，那么最后枚举完所有位 sum>=0时就是满足的，后面的位数凑足sum位就可以了。

仔细想想这个状态是与f(a)无关的(新手似乎很难理解)，一个状态只有在sum>=0时才满足，如果我们按常规的思想求f(i)的话，那么最后sum>=f(a)才是满足的条件。

```cpp
#include<cstdio>



#include<cstring>



#include<iostream>



#include<string>



 



using namespace std;



const int N=1e4+5;



int dp[12][N];



int f(int x)



{



    if(x==0) return 0;



    int ans=f(x/10);



    return ans*2+(x%10);



}



int all;



int a[12];



int dfs(int pos,int sum,bool limit)



{



    if(pos==-1) {return sum<=all;}



    if(sum>all) return 0;



    if(!limit && dp[pos][all-sum]!=-1) return dp[pos][all-sum];



    int up=limit ? a[pos] : 9;



    int ans=0;



    for(int i=0;i<=up;i++)



    {



        ans+=dfs(pos-1,sum+i*(1<<pos),limit && i==a[pos]);



    }



    if(!limit) dp[pos][all-sum]=ans;



    return ans;



}



int solve(int x)



{



    int pos=0;



    while(x)



    {



        a[pos++]=x%10;



        x/=10;



    }



    return dfs(pos-1,0,true);



}



int main()



{



    int a,ri;



    int T_T;



    int kase=1;



    scanf("%d",&T_T);



    memset(dp,-1,sizeof dp);



    while(T_T--)



    {



        scanf("%d%d",&a,&ri);



        all=f(a);



        printf("Case #%d: %d\n",kase++,solve(ri));



    }



    return 0;



}
```

 

减法的艺术！！！

 

例题 **[POJ 3252](http://poj.org/problem?id=3252)**

这题的约束就是一个数的二进制中0的数量要不能少于1的数量，通过上一题，这题状态就很简单了，dp[pos][num],到当前数位pos,0的数量减去1的数量不少于num的方案数，一个简单的问题，中间某个pos位上num可能为负数(这不一定是非法的，因为我还没枚举完嘛，只要最终的num>=0才能判合法，中途某个pos就不一定了)，这里比较好处理，Hash嘛，最小就-32吧(好像),直接加上32，把32当0用。这题主要是要想讲一下lead的用法，显然我要统计0的数量，前导零是有影响的。至于!lead&&!limit才能dp，都是类似的，自己慢慢体会吧。

```cpp
#pragma comment(linker, "/STACK:10240000,10240000")



#include<iostream>



#include<cstdio>



#include<cstring>



#include<string>



#include<queue>



#include<set>



#include<vector>



#include<map>



#include<stack>



#include<cmath>



#include<algorithm>



using namespace std;



const double R=0.5772156649015328606065120900;



const int N=1e5+5;



const int mod=1e9+7;



const int INF=0x3f3f3f3f;



const double eps=1e-8;



const double pi=acos(-1.0);



typedef long long ll;



int dp[35][66];



int a[66];



int dfs(int pos,int sta,bool lead,bool limit)



{



    if(pos==-1)



        return sta>=32;



    if(!limit && !lead && dp[pos][sta]!=-1) return dp[pos][sta];



    int up=limit?a[pos]:1;



    int ans=0;



    for(int i=0;i<=up;i++)



    {



        if(lead && i==0) ans+=dfs(pos-1,sta,lead,limit && i==a[pos]);//有前导零就不统计在内



        else ans+=dfs(pos-1,sta+(i==0?1:-1),lead && i==0,limit && i==a[pos]);



    }



    if(!limit && !lead ) dp[pos][sta]=ans;



    return ans;



}



int solve(int x)



{



    int pos=0;



    while(x)



    {



        a[pos++]=x&1;



        x>>=1;



    }



    return dfs(pos-1,32,true,true);



}



int main()



{



    memset(dp,-1,sizeof dp);



    int a,b;



    while(~scanf("%d%d",&a,&b))



    {



        printf("%d\n",solve(b)-solve(a-1));



    }



    return 0;



}
```

然后就是一些需要自己yy的题：

**[HDU 3709](http://acm.hdu.edu.cn/showproblem.php?pid=3709) 这题就是要枚举中轴，然后数位dp**

[UVA 1305](http://acm.hust.edu.cn/vjudge/problem/38405) 这题我二分然后数位dp搞(好像不是正解，我水过的)

**[Hbzoj 1799](http://acm.hust.edu.cn/vjudge/problem/19309) 这题讲一下：**

**（偷偷告诉你，这个oj是单组测试，然后memset什么的都是浮云了）**

**约束：一个数是它自己数位和的倍数，直接dp根本找不到状态，枚举数位和，因为总就162,然后问题就变成了一个数%mod=0，mod是枚举的，想想状态：dp[pos][sum][val]，当前pos位上数位和是sum,val就是在算这个数%mod,（从高位算  \*10+i），因为我们枚举的数要保证数位和等于mod，还要保证这个数是mod的倍数，很自然就能找到这些状态，显然对于每一个mod，val不能保证状态唯一，这是你要是想加一维dp[pos][sum][val][mod],记录每一个mod的状态(这里sum可以用减法，然而val不行，就只能加一维)，那你就想太多了，这样是会超时的(因为状态太多，记忆化效果不好)。这里直接对每一个mod，memset一次就能ac。下面的代码还把limit的当做了状态，因为每次都要初始化，所以能这样，memset在多组外面是不能这样的，不过奇葩的，这代码，如果不把limit当状态，还是在!limit 条件下记录dp，提交一发，时间竟然更短了，可能是每次memset的关系！！！**

 

```cpp
#include<cstdio>



#include<cstring>



#include<iostream>



#include<string>



 



using namespace std;



 



typedef long long ll;



 



ll dp[20][163][163][2];



int a[20];



ll dfs(int pos,int sum,int val,int mod,bool limit)



{



    if(sum-9*pos-9>0) return 0;//最坏的情况，这一位及后面的全部为9都不能达到0那就直接GG，这个剪枝不会影响ac



    if(pos==-1) return sum==0 && val==0;



    if(dp[pos][sum][val][limit]!=-1) return dp[pos][sum][val][limit];



    int up=limit?a[pos]:9;



    ll ans=0;



    for(int i=0;i<=up;i++)



    {



        if(sum-i<0) break;



        ans+=dfs(pos-1,sum-i,(val*10+i)%mod,mod,limit && i==a[pos]);



    }



    dp[pos][sum][val][limit]=ans;



    return ans;



}



ll solve(ll x)



{



    int pos=0;



    while(x)



    {



        a[pos++]=x%10;



        x/=10;



    }



    ll ans=0;



    for(int i=1;i<=pos*9;i++)//上限就是每一位都是9



    {



        memset(dp,-1,sizeof dp);



        ll tmp=dfs(pos-1,i,0,i,true);



        ans+=tmp;



    }



    return ans;



}



int main()



{



//    cout<<18*9<<endl;



    ll le,ri;



//    memset(dp,-1,sizeof dp);



    while(~scanf("%lld%lld",&le,&ri))



        printf("%lld\n",solve(ri)-solve(le-1));



    return 0;



}



/*



1 1000000000000000000



*/
```

基本讲的差不多了。前段时间学了点新东西！！

 

# 新的领域--计数转求和

[HDU 4507](http://acm.hdu.edu.cn/showproblem.php?pid=4507)

这题麻烦就是要求数的平方和。

我们先考虑求和的问题，一个区间，数位dp能在一些约束下计数，现在要这些数的和。其实组合数学搞搞就可以了：如 现在枚举的某一位pos,我统计了这一位枚举i的满足条件的个数cnt，其实只要算i对总和的贡献就可以了，对于一个数而言第pos位是i，那么对求和贡献就是i*10^pos,就是十进制的权值，然后有cnt个数都满足第pos位是i，最后sum=cnt*i*10^pos.原理就是这样平方和可以看做(a*10^pos+b)^2,a是你当前pos位要枚举的，b其实是个子问题，就是pos之后的位的贡献值，把这个平方展开就可以了！

```cpp
#pragma comment(linker, "/STACK:10240000,10240000")



#include<iostream>



#include<cstdio>



#include<cstring>



#include<string>



#include<queue>



#include<set>



#include<vector>



#include<map>



#include<stack>



#include<cmath>



#include<algorithm>



using namespace std;



const double R=0.5772156649015328606065120900;



const int N=1e5+5;



const int mod=1e9+7;



const int INF=0x3f3f3f3f;



const double eps=1e-8;



const double pi=acos(-1.0);



typedef long long ll;



ll fact[20];



void init()



{



    fact[0]=1;



    for(int i=1;i<20;i++)



        fact[i]=fact[i-1]*10%mod;



}



struct node



{



    ll cnt,sum,sqr;



    node(ll cnt=-1,ll sum=0,ll sqr=0):cnt(cnt),sum(sum),sqr(sqr){}



}dp[20][7][7];



int a[20];



ll fac(ll x)



{



    return x*x%mod;



}



ll dfs(int pos,ll num,ll val,ll&cnt,ll&sum,bool limit)



{



    if(pos==-1) {



        if(num==0 || val==0)



            return 0;



        cnt=1;



        return 0;



    }



    if(!limit && dp[pos][num][val].cnt!=-1) {



            cnt=dp[pos][num][val].cnt;



            sum=dp[pos][num][val].sum;



            return dp[pos][num][val].sqr;



    }



    int up=limit?a[pos]:9;



    ll sq=0;



    for(int i=0;i<=up;i++)



    if(i!=7)



    {



        ll cn=0,su=0;



        ll tmp=dfs(pos-1,(num+i)%7,(val*10+i)%7,cn,su,limit && i==a[pos]);



        ll tm=i*fact[pos]%mod;



        tmp=(tmp+fac(tm)*cn%mod+(tm*su%mod)*2%mod)%mod;//计数之后要更新sum,sqr



        sum=(sum+su+(i*fact[pos]%mod)*cn%mod)%mod;



        cnt=(cnt+cn)%mod;



        sq=(sq+tmp)%mod;



    }



    if(!limit) dp[pos][num][val]=node(cnt,sum,sq);



    return sq;



}



ll solve(ll x)



{



    int pos=0;



    while(x)



    {



        a[pos++]=x%10;



        x/=10;



    }



    ll t1=0,t2=0;



    return dfs(pos-1,0,0,t1,t2,true);



}



bool judge(ll x)



{



    int sum=0;



    int pos=0;



    if(x%7==0) return false;



    while(x)



    {



        if(x%10==7) return false;



        sum+=x%10;



        x/=10;



    }



    sum%=7;



    return sum!=0;



}



int main()



{



    init();



    for(int i=0;i<20;i++)



        for(int j=0;j<7;j++)



        for(int k=0;k<7;k++)//memset



    {



        dp[i][j][k].cnt=-1;



        dp[i][j][k].sum=0;



        dp[i][j][k].sqr=0;



    }



    int T_T;



    scanf("%d",&T_T);



    while(T_T--)



    {



        ll le,ri;



        scanf("%I64d%I64d",&le,&ri);



        ll ans=solve(ri)-solve(le-1);



        ans=(ans%mod+mod)%mod;



        printf("%I64d\n",ans);



    }



    return 0;



}
```

 