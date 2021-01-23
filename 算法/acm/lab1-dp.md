## A - Charlie's Change

Sample Input

```
12 5 3 1 2
16 0 0 0 1
0 0 0 0 0
```

Sample Output

```
Throw in 2 cents, 2 nickels, 0 dimes, and 0 quarters.
Charlie cannot buy coffee.
```

题目大概意思：

用硬币1,5,10,25装满背包P,硬币有数量限制，要求装尽可能多的硬币，并且输出每种硬币使用的个数

**分析**

首先这是一道特殊的完全背包问题

- 每种硬币数量有限制
- 要输出硬币使用的个数

所以需要额外增加两个数组used[j]与path[j]

used[j]代表在当前硬币i，装满j所需要的i的个数

```
used[j]=used[j-coins[i]]+1
//需要在每次更换硬币i时memset，否则会和上一次的循环产生重叠
```

path[j]代表背包j的上一个路径，那么想要求某一个硬币的个数的话：

```
path[j]=j-coins[i]

ans[j-path[j]]++
```

以上就是完全背包+数量限制+路径记忆的解决方法：

```c++
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
using namespace std;
const int INF=0x3f3f3f3f;
int v[4]= {1,5,10,25};
int dp[10010];
int path[10010],used[10010];
int num[4];
int ans[100];//这个要大于25
int main()
{
 //   freopen("in.txt","r",stdin);
//   freopen("out.txt","w",stdout);
    int P;
    while(scanf("%d%d%d%d%d",&P,&num[0],&num[1],&num[2],&num[3]),(P+num[0]+num[1]+num[2]+num[3]))
    {
        for(int i=0; i<=P; i++)dp[i]=-INF;
        memset(path,0,sizeof(path));

        path[0]=-1;//退出的条件
        dp[0]=0;
        for(int i=0; i<4; i++)
        {
            memset(used,0,sizeof(used));
            for(int j=v[i]; j<=P; j++)
            {
                if(dp[j-v[i]]+1>dp[j]&&dp[j-v[i]]>=0&&used[j-v[i]]<num[i])
                {
                    //dp[j]存j里用多少枚硬币换。(要多的)；used来记录是否超过数量
                    dp[j]=dp[j-v[i]]+1;//压入dp，
                    used[j]=used[j-v[i]]+1;//体积j,里数量加一
                    path[j]=j-v[i];//记录上一个路径（体积）
                }
  //              printf("dp[%d]=%d \t used[%d]=%d\tpath[%d]=%d\n",j,dp[j],j,used[j],j,path[j]);
            }
        }

        if(dp[P]<0)
        {
            printf("Charlie cannot buy coffee.\n");
            continue;
        }

        memset(ans,0,sizeof(ans));
        int i=P;
        while(1)
        {
            if(path[i]==-1)break;
            ans[i-path[i]]++;
            i=path[i];
        }
        printf("Throw in %d cents, %d nickels, %d dimes, and %d quarters.\n",ans[v[0]],ans[v[1]],ans[v[2]],ans[v[3]]);

    }
    return 0;
}
/*
12 5 3 1 2
16 0 0 0 1
0 0 0 0 0
*/
```

## B - Dividing

一道典型的子集背包问题

题意：

大理石的值为1-6

给出每种类型大理石的数量，问能不能划分一半

```c++
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
using  namespace std;
int coins[]={1,2,3,4,5,6};
int nums[7]={0};
int used[200002]={0};
int dp[200002]={0};
int main()
{
    int p;
    int task=0;
    while(cin>>nums[0]&&cin>>nums[1]&&cin>>nums[2]&&cin>>nums[3]&&cin>>nums[4]&&cin>>nums[5])
    {

        int target=0;
        for(int i=0;i<6;i++)
        {
            target+=nums[i]*coins[i];
        }
        if(target==0)
            break;
        printf("Collection #%d:\n", ++task);
        if(target%2==1)
        {
            printf("Can't be divided.\n");
            cout<<endl;
            continue;
        }
        target=target/2;
        memset(dp,0,sizeof(dp));
        dp[0]=1;
        for(int i=0;i<6;i++)
        {
            memset(used,0,sizeof(used));
            for(int j=coins[i];j<=target;j++)
            {
                if(!dp[j]&&dp[j-coins[i]]&&used[j-coins[i]]<nums[i])
                {
                    dp[j]=1;
                    used[j]=used[j-coins[i]]+1;
                }
            }
        }
        if(dp[target])
        {
            printf("Can be divided.\n");
        }
        else{
            printf("Can't be divided.\n");
        }
        cout<<endl;

    }
}
```

## C - Space Elevator

Sample Input

```
3
7 40 3
5 23 8
2 52 6
```

Sample Output

```
48
```

**题目大意**

通过用每种型号的模块构建最高的塔； 每种模块高度不同，数量有限，并且在空间中存在的高度有限

首先看到这种数量有限的字眼，就应该想到完全背包问题

其次，高度有限，而动态规划的核心在于模拟以及状态的选择的

总不能模拟无穷的高度吧？所以二重循环就模拟每个模块的限制高度

然后配合use数组即可

```c++
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
struct node
{
    int h,a,c;
};
int f[40010],v[40010];//f数组中存放几种类型模块可搭建的高度，v数组存放块数
int cmp(node s,node b)
{
    return s.a<b.a;
}
int main()
{
    int n;
    scanf("%d",&n);
    node num[n];
    for(int i=0;i<n;i++)
        scanf("%d %d %d",&num[i].h,&num[i].a,&num[i].c);
    sort(num,num+n,cmp);
    memset(f,0,sizeof(f));
    f[0]=1;
    int ans=0;
    for(int i=0;i<n;i++)
    {
        memset(v,0,sizeof(v));
        for(int j=num[i].h;j<=num[i].a;j++)
        {
            if(!f[j]&&f[j-num[i].h]&&v[j-num[i].h]<num[i].c)
            {
                f[j]=1;
                v[j]=v[j-num[i].h]+1;
                if(ans<j)
                    ans=j;
                //printf("j-num[%d].h=%d num[%d].h=%d\n",i,j-num[i].h,i,num[i].h);
                //printf("ans=%d\n",ans);
            }
        }
    }
    printf("%d\n",ans);
    return 0;
}
```

## E - 不要62

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

## F - F(x)

例题：[HDU 4734](http://acm.hdu.edu.cn/showproblem.php?pid=4734)

题目给了个f(x)的定义：F(x) = An * 2^(n-1) + An-1 * 2^(n-2) + ... +*1，Ai是十进制数位，然后给出a,b求区间[0,b]内满足f(i)<=f(a)的i的个数。

常规想：这个f(x)计算就和数位计算是一样的，就是加了权值，所以dp[pos][sum]，这状态是基本的。a是题目给定的，f(a)是变化的不过f(a)最大好像是4600的样子。如果要memset优化就要加一维存f(a)的不同取值，那就是dp[10][4600][4600]，这显然不合法。

这个时候就要用减法了，dp[pos][sum]，sum不是存当前枚举的数的前缀和(加权的)，而是枚举到当前pos位，后面还需要凑sum的权值和的个数，

也就是说初始的是时候sum是f(a),枚举一位就减去这一位在计算f(i)的权值，那么最后枚举完所有位 sum>=0时就是满足的，后面的位数凑足sum位就可以了。

仔细想想这个状态是与f(a)无关的(新手似乎很难理解)，一个状态只有在sum>=0时才满足，如果我们按常规的思想求f(i)的话，那么最后sum>=f(a)才是满足的条件。

可以联想一下背包问题

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

## H-XHXJ的LIS

## K - GAME

有N个房间。房间的连接就像一棵树。换句话说，您只能以一种方式去任何其他房间。每个房间都有一份为您准备的礼物，如果您去房间，就可以得到这份礼物。但是，某些房间也有陷阱。收到礼物后，您可能会被困住。外出一个房间后，您将无法再回到它。您可以选择在任何房间开始，而当您没有空间离开或被困C时间时，游戏结束。现在，您想知道可获得的礼物最大总价值是多少。

输入值

　　第一行包含一个整数T，表示测试用例的数量。
　　对于每个测试用例，第一行包含一个整数N（2 <= N <= 50000），数个房间和另一个整数C（1 <= C <= 3），即被捕获的几率。接下来的N行中的每行包含两个整数，它们是房间中的礼物值以及该房间中是否有陷阱。房间的编号从0到N-1。接下来的N-1条线中的每条线都包含两个整数A和B（0 <= A，B <= N-1），表示房间A和房间B已连接。
　　**所有礼物的价值均大于0。**

输出量

　　对于每个测试用例，输出可以获得的最大礼物总价值。

样本输入

```
2
3 1
23 0
12 0
123 1
0 2
2 1

3 2
23 0
12 0
123 1
0 2
2 1
```

样本输出

```
146
158
```

- 状态

当前在哪个房间i,房间困住的时间j





































