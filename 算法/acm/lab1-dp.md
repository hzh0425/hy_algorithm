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